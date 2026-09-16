# 何时使用模拟

只在**系统边界**处进行模拟：

- 外部 API（支付、电子邮件等）
- 数据库（有时需要，但优先使用测试数据库）
- 时间或随机性
- 文件系统（有时需要）

不要模拟：

- 自己的类或模块
- 内部协作者
- 任何由你控制的内容

## 为可模拟性而设计

在系统边界处，设计易于模拟的接口：

**1. 使用依赖注入**

从外部传入依赖，而不是在内部创建它们：

```typescript
// 易于模拟
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// 难以模拟
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 优先使用 SDK 风格接口，而不是通用请求器**

为每个外部操作创建专用函数，不要使用一个包含条件逻辑的通用函数：

```typescript
// 好：每个函数都可以独立模拟
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// 坏：模拟时需要在模拟实现内部加入条件逻辑
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 方式意味着：

- 每个模拟只返回一种特定的数据形状
- 测试设置中没有条件逻辑
- 更容易看出测试覆盖了哪些端点
- 每个端点都能获得类型安全
