## Xử lý lỗi tập trung

- Gom tất cả logic xử lý lỗi vào 1 object chuyên dụng duy nhất (Centralized Error Handler). Trong đó: quản lý cách ứng dụng phản ứng với lỗi (như ghi log ra sao, có bắn cảnh báo không, quyết định có dừng chương trình không). → Tất cả những nơi khác gọi đến khi xảy ra lỗi.
- Các web framework (như express.js) thường cung cấp middleware bắt lỗi. Practice tốt là: Middleware này chỉ đóng vai trò chuyển tiếp, nhận lỗi rồi gọi tới cái Error Handler tập trung của mình.
- Nếu không xử lý tập trung → code lặp lại, logic xử lý không thống nhất.

### BAD:

**DAL layer, không xử lý lỗi ở đây**

```
DB.addDocument(newCustomer, (error: Error, result: Result) => {
    if (error)
        throw new Error('Great error explanation comes here', other useful parameters)
});
```

**API route code, bắt cả lỗi đồng bộ và bất đồng bộ và chuyển tiếp đến middleware**

```
try {
  customerService
    .addNew(req.body)
    .then((result: Result) => {
      res.status(200).json(result);
    })
    .catch((error: Error) => {
      next(error);
    });
} catch (error) {
  next(error);
}
```

**Error handling middleware, chuyển tiếp xử lý lỗi đến centralized error handler**

```
app.use(async (err: Error, req: Request, res: Response, next: NextFunction) => {
  await errorHandler.handleError(err, res);
});

process.on("uncaughtException", (error: Error) => {
  errorHandler.handleError(error);
});

process.on("unhandledRejection", (reason) => {
  errorHandler.handleError(reason);
});


```

### WORSE: xử lý lỗi trong middlewares.

**middleware xử lý lỗi trực tiếp, ai sẽ xử lý Cron jobs và lỗi kiểm thử?**

```
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  logger.logError(err);
  if (err.severity == errors.high) {
    mailer.sendMail(configuration.adminMail, "Critical error occured", err);
  }
  if (!err.isOperational) {
    next(err);
  }
});
```

### GOOD: xử lý lỗi trong một dedicated object.

```
class ErrorHandler {
  public async handleError(
    error: Error,
    responseStream: Response,
  ): Promise<void> {
    await logger.logError(error);
    await fireMonitoringMetric(error);
    await crashIfUntrustedErrorOrSendResponse(error, responseStream);
  }
}

export const handler = new ErrorHandler();
```
