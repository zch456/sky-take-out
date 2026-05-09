# Prompt: 购物车模块

## 使用阶段
项目第 7-8 天

## Prompt

> 购物车用 Redis Hash 实现，不使用 MySQL 表：
> 
> **数据结构：**
> - Key：`cart:{userId}`
> - Field：`{dishId}` 或 `{setmealId}`
> - Value：CartItemDTO JSON（name, image, amount, quantity, dishId/setmealId）
> 
> **接口（userId 从 ThreadLocal 取，接口不传参）：**
> - POST /api/user/shopping-cart/add —— 添加商品，已存在 field 则 quantity+1
> - POST /api/user/shopping-cart/sub —— 减少数量，quantity 为 0 时删除 field
> - DELETE /api/user/shopping-cart/clean —— 清空购物车
> - GET /api/user/shopping-cart/list —— 列出全部商品
> 
> **约束：**
> - quantity 不能为负
> - 菜品被下架时，list 接口对已下架菜品标记 status=0
> - 所有操作使用 RedisTemplate 完成

## 设计决策
选择 Redis Hash 而非 MySQL 表的原因：
1. 购物车高频读写，DB 写压力大
2. 数据量小（用户维度），Redis 内存开销可接受
3. 数据丢失可接受（购物车是临时状态，非关键业务数据）
4. Hash 结构天然支持「用户 → 多商品」映射
