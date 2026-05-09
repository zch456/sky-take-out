# 校园外卖平台

基于 Spring Boot 的校园外卖点餐平台，涵盖用户端（微信小程序）与后台管理端。**本项目全程使用 Claude Code 作为 AI 开发伙伴**，探索并实践了 AI 驱动的软件工程工作流。

## AI 驱动开发

```
需求拆分 → Prompt 设计 → AI 生成代码 → 人工 Review → 修正 → 集成测试
```

- **架构决策人定，编码执行 AI 做，安全与一致性人把关**
- 每个模块平均 2-3 轮「生成 → Review → 修正」循环
- 累计交付可用接口 30+，传统方式预估 2 个月，AI 协作模式下约 1 个月

详见 [`.claude/prompts/`](./.claude/prompts/) 中的 Prompt 模板库，以及 [CLAUDE.md](./CLAUDE.md) 中的项目配置。

## 技术栈

| 层级 | 技术 |
|------|------|
| 框架 | Spring Boot 2.7.3 + MyBatis + Spring MVC |
| 认证 | JWT + Redis 双令牌管理 + ThreadLocal 上下文 |
| 数据库 | MySQL 8.0 + Druid 连接池 + PageHelper 分页 |
| 缓存 | Redis（验证码、购物车 Hash、登录态、AI 分析缓存） |
| 消息 | RabbitMQ（异步判题、通知） |
| 支付 | 微信支付 JSAPI V3（签名、回调验签、幂等处理） |
| 前端 | 微信小程序（UniApp）+ 后台管理端（Vue + Element UI 模板） |
| 工具 | Knife4j 接口文档、阿里云 OSS 文件上传、POI Excel 导出 |
| AI 工具链 | Claude Code、Cursor、ChatGPT |

## 模块概览

```
sky-take-out/
├── sky-common/        # 公共模块（工具类、常量、异常、拦截器、AOP）
├── sky-pojo/          # 实体与 DTO（Entity、DTO、VO）
├── sky-server/        # 服务端（Controller、Service、Mapper、配置）
└── mp-weixin/         # 微信小程序前端（UniApp）
```

### 核心业务

| 模块 | 说明 | 技术要点 |
|------|------|---------|
| 用户认证 | 短信验证码登录 + JWT 签发 + 拦截器 | Redis 缓存验证码、ThreadLocal 上下文传递 |
| 菜品管理 | CRUD + 分类 + 多规格 + 起售/停售 | 套餐与单菜品关联、口味管理 |
| 购物车 | 添加/减少/清空，用户端独立 | Redis Hash 结构，降低 DB 频写压力 |
| 订单 | 下单 + 库存扣减 + 状态流转 + 支付 | @Transactional 保证一致性、WHERE stock ≥ qty 乐观锁防超卖 |
| 地址簿 | 用户地址管理 | 默认地址切换 |
| 支付 | 微信支付 V3 + 回调 | 签名校验 + AES 解密 + 幂等处理 |
| 后台管理 | 菜品/套餐/订单/员工管理 + RBAC | 文件上传 OSS、Excel 导出、权限注解拦截 |

## 快速开始

```bash
# 1. 导入数据库
mysql -u root -p < sky.sql

# 2. 修改配置
# sky-server/src/main/resources/application-dev.yml
# 填入 MySQL、Redis、微信支付、阿里云 OSS 配置

# 3. 启动服务
cd sky-server
mvn spring-boot:run

# 4. 接口文档
# http://localhost:8080/doc.html
```

> 微信支付回调需要内网穿透工具（如 frp/ngrok）暴露本地 8080 端口。

## License

MIT
