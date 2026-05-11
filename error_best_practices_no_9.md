## Phát hiện lỗi và thời gian chết (downtime) bằng các công cụ APM

- APM (Application Performance Management) là gì ? Là các phần mềm chuyên dụng dùng để giám sát hiệu suất ứng dụng được gắn trực tiếp vào ứng dụng Nodejs.
- Nó giúp:
  - Tự động gom nhóm các lỗi giống nhau để file log không bị dài dằng dặc, khó tìm nguyên nhân gốc rễ
  - Tự động phát hiện Memory Leak và đo lường độ trễ của Event Loop - 2 nguyễn nhân thường gây sập server Nodejs. Ngoài ra, APM còn cảnh báo khi API trả về lỗi, phát hiện thời gian phản hồi bất thường của API, …
  - Cảnh báo downtime: Nếu server ngừng phản hổi, APM sẽ gửi cảnh báo qua Slack, Email,...
- Một số APM phổ biến như: Sentry, Datadog, New Relic, …

#### BAD:

```typescript
// No APM (Application Performance Management) integration

// The server crashes silently, and the team only finds out when users complain or manually check logs
```

#### GOOD:

```typescript
// APM (Application Performance Management) integration using Sentry

import * as Sentry from "@sentry/node";

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  tracesSampleRate: 1,
});

app.use(Sentry.Handlers.requestHandler());

// ... route handlers ...

app.use(Sentry.Handlers.errorHandler());

// The team gets notified immediately when errors occur
// Users see helpful error messages instead of blank screens
// Root causes are automatically grouped and prioritized
```
