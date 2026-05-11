## LUÔN SỬ DỤNG ERROR BUILT-IN VÀ INSTANCE CỦA NÓ KHI THROW ERROR.

#### KHÔNG THỂ TRẢ VỀ STRING HOẶC CÁC TYPE KHÁC.

#### BỞI VÌ NÓ SẼ LÀM MẤT STACK TRACE VÀ GÂY KHÓ KHĂN KHI DEBUG.

### BAD:

```typescript
if (!productToAdd) throw "How can I add new product when no value provided?";
```

### GOOD:

```typescript
if (!productToAdd)
  throw new Error("How can I add new product when no value provided?");
```

### BETTER:

```typescript
import { StatusCode } from "http-status-codes";

export class AppError extends Error {
  public readonly name: string;
  public readonly httpCode: StatusCode;
  public readonly isOperational: boolean;

  constructor(
    name: string,
    httpCode: StatusCode,
    description: string,
    isOperational: boolean,
  ) {
    super(description);

    Object.setPrototypeOf(this, new.target.prototype); // restore prototype chain

    this.name = name;
    this.httpCode = httpCode;
    this.isOperational = isOperational;

    Error.captureStackTrace(this);
  }
}

if (!productToAdd)
  throw new AppError(
    "BAD_REQUEST",
    StatusCode.BAD_REQUEST,
    "How can I add new product when no value provided?",
    true,
  );
```
