## Bắt các promise bị reject mà chưa được xử lý (unhandled promise rejections)

- Nếu một hàm xử lý tác vụ bất đồng bộ mà trạng thái nó trả về là rejected nhưng lại không được xử lý vd: không được bọc trong try/catch hay không gắn .catch() thì nó sẽ trở thành Unhandled Rejection
- Việc này sẽ gây ra:
  - Ứng dụng Nodejs sẽ bị sập khi gặp unhandledRejection - sẽ dừng ngay lập tức process với exit code 1.
  - Request sẽ bị treo bởi vì lỗi xảy ra trong quá trình xử lý Request mà không được bắt lại thì Express.js sẽ không bao giờ trả về Response
- Nên làm như thế nào để tránh ?
  - Luôn sử dụng try/catch khi dùng async/await.
  - Tạo một sự kiện ở file chạy chính như index.js hay server.js để hứng tất cả những Promise Rejection để bắt tất cả các lỗi unhandledRejection nếu bị bỏ sót.
  - Không nên bắt lỗi unhandledRejection ghi log rồi để ứng dụng chạy tiếp bởi vì chúng là các programmer error - không thể đoán trước, nếu tiếp tục chúng có thể gây ra những hậu quả mà không thể nào lường trước được. Cách làm đúng là ghi log lỗi -> ngắt các kết nối -> thoát ứng dụng ( process.exit(1) ) -> dùng các công cụ quản lý process tự khởi động lại ứng dụng vd: Docker hay Kubernetes

### BAD:

```typescript
// This route will crash the server if an unhandled error occurs
app.get(
  "/users/:id",
  asyncHandler(async (req: Request, res: Response) => {
    const user = await getUserById(req.params.id);
    if (!user) {
      return res.status(404).json({ error: "User not found" });
    }
    res.json(user);
  }),
);
```

### GOOD:

```typescript
// This route uses try/catch with asyncHandler, preventing server crashes
app.get(
  "/users/:id",
  asyncHandler(async (req: Request, res: Response) => {
    try {
      const user = await getUserById(req.params.id);
      if (!user) {
        return res.status(404).json({ error: "User not found" });
      }
      res.json(user);
    } catch (error: any) {
      throw new AppError(
        "BAD_REQUEST",
        StatusCode.BAD_REQUEST,
        "Invalid user ID format",
        true,
      );
    }
  }),
);

// In index.js or server.js:
process.on("unhandledRejection", (error: any) => {
  console.error("UNHANDLED REJECTION! 💥 shutting down...");
  console.error(error.name, error.message, error.stack);
  // Close database connections, log error, exit process
  process.exit(1);
});
```
