## Lắng nghe sự kiện 'error' của event emitter và streams thay vì dùng try-catch thông thường

- Khối try/catch hoàn toàn vô dụng trước một Stream bị lỗi, nó sẽ phát ra một sự kiện có tên là ‘error’.
- Nếu một Event Emitter phát hiện sự kiện ‘error’ mà không có bất kỳ hàm <b>.on('error',...)</b> nào được gắn vào thì Node.js sẽ biến nó thành một Uncaught Exception và khởi động lại process.
- Khi làm việc với EventTargets (phiên bản tiêu chuẩn của Event Emitter trong web) không tồn tại sự kiện ‘error’, tất cả các lỗi sẽ được xử lý trong sự kiện toàn cục <b>process.on('error')</b>.

### BAD:

```typescript
// BAD EXAMPLE: Using try-catch for an EventEmitter - won't catch stream errors
try {
  const readableStream = fs.createReadStream("non_existent_file.txt");
  readableStream.on("data", (data) => console.log(data));
  readableStream.on("end", () => console.log("Stream finished successfully"));
} catch (error) {
  // This catch block will NOT catch errors from the stream
  console.error("Error caught:", error);
}
```

### GOOD:

```typescript
// GOOD EXAMPLE: Handling EventEmitter errors properly
const readableStream = fs.createReadStream("non_existent_file.txt");

// Listen for 'error' event on the stream
readableStream.on("error", (error) => {
  // This will catch the file not found error
  console.error("Stream error:", error.message);
});

readableStream.on("data", (data) => console.log(data));

readableStream.on("end", () => console.log("Stream finished successfully"));
```
