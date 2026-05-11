## PHÂN BIỆT OPERATIONAL ERRORS VÀ PROGRAMMING ERRORS

### Operational Errors:

- Là trường hợp lỗi mà bạn có thể dự đoán trước được.
- Có thể xử lý khéo léo.
- Dễ xử lý → Nên được xử lý nhanh gọn (báo lỗi cho user) mà không cần phải khởi động lại hệ thống → Đảm bảo trải nghiệm của người dùng.
- VD: lỗi DB timeout, lỗi API, input không hợp lệ.

### Programming Errors:

- Lỗi bất thường, không lường trước được, có thể kéo theo hệ quả nghiêm trọng, state của app có thể đã bị hỏng.
- VD: Cố gắng đọc giá trị của undefined, rò rỉ bộ nhớ db.
- Khi xảy ra cần phải thoát tiến trình an toàn (graceful exit) ngay lập tức và khởi động lại hệ thống.

### Ví dụ:

```typescript
//BAD:

function deleteProduct(id: number) {
  if (isNaN(id)) {
    throw "Invalid product ID"; // NOT OPERATIONAL ERROR
  }
  //...
  // ...other code
  if (productToDelete.deleted) {
    throw "Product already deleted"; // OPERATIONAL ERROR
  }
}
```

```typescript
//GOOD:

function deleteProduct(id: number) {
  if (isNaN(id)) {
    throw new AppError(
      "BAD_REQUEST",
      StatusCode.BAD_REQUEST,
      "Invalid product ID",
      true,
    );
  }
  //...
  // ...other code
  if (productToDelete.deleted) {
    throw new AppError(
      "BAD_REQUEST",
      StatusCode.BAD_REQUEST,
      "Product already deleted",
      true,
    );
  }
}
```
