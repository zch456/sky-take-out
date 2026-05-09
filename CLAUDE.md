# CLAUDE.md — 校园外卖平台

## 项目信息
- **技术栈**：Spring Boot 2.7.3 + MyBatis + MySQL + Redis + JWT + 微信支付 V3
- **结构**：多模块 Maven（sky-common / sky-pojo / sky-server）+ 微信小程序前端（mp-weixin）
- **包路径**：`com.sky.*`

## 编码规范
- 统一响应体使用 `Result<T>`（sky-common）
- 分页查询用 `PageResult<T>`，参数用 `*PageQueryDTO`
- Controller 标注 `@Slf4j`，关键操作打印参数日志
- Service 层异常统一抛出自定义异常（sky-common/exception/），由 `GlobalExceptionHandler` 统一处理
- 数据库操作使用 MyBatis 注解为主，复杂查询用 XML
- 遵循阿里巴巴 Java 开发规范

## 关键设计决策（人工拍板，AI 执行）
1. **购物车用 Redis Hash 存储**——高频读写，数据量小，丢了可重建
2. **订单库存扣减用乐观锁**——`WHERE stock >= quantity`，防止超卖
3. **JWT + Redis 双令牌**——利用 Redis TTL 天然实现单设备登录
4. **微信支付回调幂等**——根据 out_trade_no 查订单状态，已支付直接返回 200
5. **后台 RBAC 用注解拦截器**——`@AdminCheck` 注解 + 拦截器，低侵入

## 常用命令
```bash
# 启动后端
cd sky-server && mvn spring-boot:run

# 打包
mvn clean package -DskipTests

# 接口文档地址
# http://localhost:8080/doc.html
```
