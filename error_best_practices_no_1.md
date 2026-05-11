## KHÔNG XỬ LÝ LỖI TRONG HÀM CALLBACK BỞI VÌ NÓ DỄ DẪN ĐẾN CALLBACK HELL

### BAD:

```typescript
getData(someParameter, function (err: Error | null, resultA: ResultA) {
  if (err !== null) {
    // do something like calling the given callback function and pass the error
    getMoreData(resultA, function (err: Error | null, resultB: ResultB) {
      if (err !== null) {
        // do something like calling the given callback function and pass the error
        getMoreData(resultB, function (resultC: ResultC) {
          getMoreData(resultC, function (err: Error | null, d: ResultD) {
            if (err !== null) {
              // you get the idea?
            }
          });
        });
      }
    });
  }
});
```

### GOOD:

- Sử dụng async/await hoặc promise thay vì callback function là một cách tốt hơn để xử lý lỗi trong mã bất đồng bộ.
- Nó có thể giúp bạn tránh callback hell và làm cho mã của bạn dễ đọc và dễ bảo trì hơn.
- Bạn có thể sử dụng khối try/catch để xử lý lỗi trong async/await hoặc sử dụng phương thức .catch() để xử lý lỗi trong promise.

```typescript
return functionA()
  .then(functionB)
  .then(functionC)
  .then(functionD)
  .catch((err) => logger.error(err))
  .then(alwaysExecuteThisFunction);
```

**Hoặc**

```typescript
async function executeAsyncTask() {
  try {
    const valueA = await functionA();
    const valueB = await functionB(valueA);
    const valueC = await functionC(valueB);
    return await functionD(valueC);
  } catch (err) {
    logger.error(err);
  } finally {
    await alwaysExecuteThisFunction();
  }
}
```
