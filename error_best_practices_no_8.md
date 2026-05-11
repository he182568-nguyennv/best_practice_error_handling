## Kiểm thử các luồng lỗi (error flows)

- Không nên chỉ thử các trường hợp happy case. Để có độ phủ mã nguồn tốt thì nên phải test cả những luồng bị lỗi. Nếu không thì ta không thể chắc chắn rằng các luồng lỗi được xử lý đúng cách.
- Các testing framework như Mocha, Chai, Jest,... đều được hỗ trợ testing các luồng bị lỗi.

#### Khi kiểm thử luồng lỗi cần đảm bảo 3 điều sau:

- Trả về đúng status code vd: nếu input sai => 400 Bad request
- Đảm bảo trả đúng tên lỗi để FE thể hiện thông báo lỗi phù hợp
- Kiểm tra lỗi đã được ghi vào log (winston/pino) hay chưa

```typescript
describe("testing error flows", function () {
  it("should return the correct status code for invalid input", function () {
    const req = {
      body: {
        name: "",
      },
    };
    const res = {
      status: sinon.stub().returnsThis(),
      json: sinon.stub(),
    };
    const next = sinon.stub();
    errorHandler(req, res, next);
    expect(res.status.calledWith(400)).to.be.true;
  });
});
```
