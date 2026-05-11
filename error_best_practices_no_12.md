## Luôn await promise trước khi return để tránh thiếu hụt/mất dấu stacktrace (partial stacktrace)

- Nếu ta return thẳng một tác vụ bất đồng bộ mà không có await thì nó sẽ luôn trả về trạng thái là Pending một cách đồng bộ và trả về trạng thái Pending thành công, khối try đã hoàn tất và khối catch cũng đóng lại. Nếu gọi vào hàm này thì lỗi gọi vào tác vụ bất động đồng bộ là chuyện đương nhiên nhưng nó sẽ không chạy vào khối catch mà nó sẽ là một Unhandled Promise Rejection.
- Luôn return await trong try/catch để catch có thể hứng được lỗi của một tác vụ bất đồng bộ thì nó phải đứng đợi tác vụ đó hoàn thành hoặc thất bại
- Nên dùng return await ngay cả khi ngoài khối try/catch để V8 Engine có thể lưu lại stack trace

### BAD:

```typescript
// This function returns the promise without await, causing partial stack traces
function getUserById(id: string) {
  // The promise is created but not awaited, so try/catch won't catch errors here
  return UserModel.findById(id);
}

async function getUserWithPartialStacktrace(id: string) {
  try {
    // Missing await before return
    return getUserById(id); // This returns a pending promise immediately
  } catch (error) {
    // This catch block won't execute for errors in getUserById
    logger.error(error);
  }
}
```

### GOOD:

```typescript
// This function awaits the promise before returning, ensuring full stack traces
async function getUserById(id: string) {
  try {
    // Await the promise to ensure errors are caught in the try/catch block
    const user = await UserModel.findById(id);
    if (!user) {
      throw new Error("User not found");
    }
    return user;
  } catch (error: any) {
    throw new AppError(
      "BAD_REQUEST",
      StatusCode.BAD_REQUEST,
      "Invalid user ID format",
      true,
    );
  }
}

// This function also uses await for better stack trace retention
async function getUserWithFullStacktrace(id: string) {
  try {
    // Always await promises for proper error handling and stack traces
    const user = await getUserById(id);
    return user;
  } catch (error: any) {
    throw new AppError(
      "BAD_REQUEST",
      StatusCode.BAD_REQUEST,
      "Invalid user ID format",
      true,
    );
  }
}
```
