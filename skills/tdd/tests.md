# 好测试与坏测试

## 好测试

**集成测试风格**：通过真实接口进行测试，而不是模拟内部组成部分。

```typescript
// 好：测试可观察行为
test("用户可以使用有效购物车结账", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

特征：

- 测试用户或调用方关心的行为
- 只使用公共 API
- 能经受内部重构
- 描述“做什么”，而不是“怎么做”
- 每个测试只包含一个逻辑断言

## 坏测试

**实现细节测试**：与内部结构耦合。

```typescript
// 坏：测试实现细节
test("checkout 会调用 paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

危险信号：

- 模拟内部协作者
- 测试私有方法
- 断言调用次数或调用顺序
- 行为未改变，仅重构便导致测试失败
- 测试名称描述“怎么做”，而不是“做什么”
- 绕过接口，改用外部手段验证

```typescript
// 坏：绕过接口进行验证
test("createUser 会保存到数据库", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// 好：通过接口进行验证
test("createUser 创建的用户可以被取回", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

**同义反复的测试**：期望值重复实现逻辑，因此测试按构造必然通过。

```typescript
// 坏：以代码计算结果的相同方式重新计算期望值
test("calculateTotal 对商品项求和", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// 好：期望值是独立且已知正确的字面量
test("calculateTotal 对商品项求和", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
