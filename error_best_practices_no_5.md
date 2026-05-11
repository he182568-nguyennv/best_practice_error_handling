## Tài liệu hóa lỗi API chi tiết, không chỉ message. Sử dụng OpenAPI (aka Swagger) hoặc GraphQL

- Document rõ các lỗi API.
- Documentation framework cho RESTful APIs là OpenAPI (Swagger). Nếu cần schema và comment thì dùng công cụ của GraphQL.
- Mục tiêu là để người gọi API (có thể là chính mình ở service khác, hoặc frontend) biết được mình có thể nhận được những lỗi nào. Từ đó có thể xử lý lỗi đúng đắn thay vì bị crash app đột ngột do không hiểu lỗi gì trả về.

### Ví dụ:

#### BAD:

```typescript
{
  "error": "Invalid product ID",
}
```

#### GOOD:

```typescript
{
  "error": {
    "name": "BAD_REQUEST",
    "httpCode": 400,
    "isOperational": true,
    "message": "Invalid product ID",
  },
}
```
