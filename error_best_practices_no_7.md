## Dùng logger chuyên dụng (mature logger) để hiển thị lỗi rõ ràng

- Dùng logger xịn (thư viện như Pino hay Winston) thay vì console.log để theo dõi và debug hệ thống.
- Các thư viện này cung cấp format chuẩn, log-levels, màu sắc; cho phép đính kèm thuộc tính custom vào log mà không làm chậm hệ thống. Tiết kiệm thời gian khi kiểm tra hệ thống.
- Nếu chỉ dùng console.log thì thuần text, bừa bộn, không thể lọc (query), làm tốn nhiều thời gian dò lỗi.

### BAD:

```typescript
const logError = (error: Error) => {
  console.log(
    JSON.stringify({
      name: error.name,
      stack: error.stack,
      message: error.message,
    }),
  );
};
```

### GOOD:

```typescript
const logError = (error: Error) => {
  const logPayload: Log = {
    message: "Error: " + error.message,
    name: error.name,
    severity: error.name === "BAD_REQUEST" ? 2 : 1,
    stack: error.stack,
    error: error,
  };
  logger.error(logPayload);
};

logger.log(error, logPayload);
```
