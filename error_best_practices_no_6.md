## Thoát tiến trình an toàn (graceful exit) khi gặp lỗi lạ không lường trước

- Thoát ứng dụng an toàn khi gặp lỗi Catastrophic (Programmer errors) → hệ thống rơi vào trạng thái bất định.
- Phải đóng các kết nối đang mở (database), hiển thị lỗi rõ ràng cho người dùng, log, sau đó thoát tiến trình → an toàn.
- Các dịch vụ hạ tầng (như Docker hay Serverless) thường sẽ tự lo việc khởi động lại sau khi exit process
- Nếu cứ để app chạy tiếp khi gặp lỗi lạ → một số thành phần có thể đã bị hỏng ngầm (VD: thư viện nội bộ chết), làm cho tất cả request sau đó bị thất bại hoặc app bị loạn, kéo theo một đống lỗi khác.

```typescript
const mongoose = require("mongoose");

mongoose.connect(config.database.url, { useUnifiedTopology: true });

mongoose.connection.on("error", (error) => {
  console.log(error);
  logger.error(error);
  process.exit(1);
});
```
