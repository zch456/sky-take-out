# Prompt: 订单模块 + 防超卖

## 使用阶段
项目第 9-12 天

## Prompt

> 下单接口 POST /api/user/order/submit：
> 
> **流程：**
> 1. 从 Redis 购物车取数据（key: `cart:{userId}`）
> 2. 校验地址簿是否存在、购物车是否为空
> 3. 扣减库存：`UPDATE dish SET stock = stock - #{qty} WHERE id = #{id} AND stock >= #{qty}`
>    - 受影响行数为 0 则抛出 OrderBusinessException("库存不足")
> 4. 生成订单主记录（order 表）和订单明细（order_detail 表）
>    - 订单号用雪花算法（IdUtil.getSnowflake）
>    - 订单状态枚举：PENDING_PAY → PAID → CONFIRMED → DELIVERY_IN_PROGRESS → COMPLETED
> 5. 清空购物车
> 6. 返回 OrderSubmitVO（orderId, orderNumber, amount, orderTime）
> 
> **约束：**
> - 以上操作全部包在 @Transactional(rollbackFor = Exception.class) 中
> - 库存扣减 SQL 必须用乐观锁（WHERE stock >= quantity）
> - 雪花算法 workerId 和 datacenterId 从配置文件读取

## AI 生成问题与修正记录（第 1 轮）
- **问题**：第一版没有处理库存并发竞争，直接 `stock = stock - qty`
- **修正**：要求 AI 改为乐观锁 SQL，加上 `AND stock >= #{qty}` 条件
- **验证**：压测 100 并发下单同一菜品，无超卖

## AI 生成问题与修正记录（第 2 轮）
- **问题**：事务只标注了 @Transactional，未指定 rollbackFor
- **修正**：加上 `rollbackFor = Exception.class`，确保所有异常都回滚

## 设计决策
选择乐观锁而非分布式锁的原因：
1. 并发量预估不高（校园场景，非秒杀）
2. 乐观锁依赖 DB 行锁，实现简单
3. 分布式锁引入 Redis 额外依赖，增加故障点
