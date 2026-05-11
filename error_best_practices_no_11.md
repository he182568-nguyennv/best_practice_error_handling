## Fail fast. Dùng thư viện để validate đầu vào (arguments) từ sớm

- Dùng thư viện như zod để validate đầu vào từ sớm để tránh dữ liệu rác lọt vào DB
- Nên dùng thư viện zod chứ không nên dùng if/else để tránh việc code sẽ bị dài dòng, khó bảo trì, khó tái sử dụng

### BAD:

```typescript
async function createUser(
  name: string,
  email: string,
  age: number,
  role: string,
) {
  if (!name || name.length > 50) {
    throw new Error("Invalid name");
  }
  if (!email || !email.includes("@")) {
    throw new Error("Invalid email");
  }
  if (!age || age > 120 || age < 0) {
    throw new Error("Invalid age");
  }
  if (!role || !["USER", "ADMIN"].includes(role)) {
    throw new Error("Invalid role");
  }
  // Create user
  return {
    name,
    email,
    age,
    role,
    createdAt: new Date(),
  };
}
```

### GOOD:

```typescript
import z from "zod";

const userSchema = z.object({
  name: z.string().min(1).max(50),
  email: z.string().email(),
  age: z.number().int().positive().max(120),
  role: z.enum(["USER", "ADMIN"]),
});

async function createUser(userData: any) {
  // Validate data early using Zod
  const result = userSchema.safeParse(userData);

  if (!result.success) {
    throw new AppError(
      "BAD_REQUEST",
      StatusCode.BAD_REQUEST,
      "Invalid input data",
      true,
    );
  }

  const { name, email, age, role } = result.data;

  // Create user
  return {
    name,
    email,
    age,
    role,
    createdAt: new Date(),
  };
}
```
