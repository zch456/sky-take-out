# Prompt: 用户认证体系

## 使用阶段
项目第 4-6 天，分三步推进

## Step 1：验证码发送

> 生成短信验证码接口：
> - POST /api/user/send-code，入参 phone
> - 使用 Hutool 生成 6 位随机数字
> - 存入 Redis，key 为 `sms:login:{phone}`，TTL 300 秒
> - 返回 Result\<String\>，提示"验证码已发送"
> - 同一手机号 60 秒内不可重复发送（Redis 存在则拒绝）

## Step 2：登录 + JWT 签发

> 用户登录接口：
> - POST /api/user/login，入参 phone + code
> - 校验 Redis 中验证码，不匹配/过期返回 LoginFailedException
> - 通过后签发 JWT（payload 含 userId，TTL 7200 秒）
> - Token 同时存入 Redis `login:token:{userId}`，实现双令牌管理
> - 返回 UserLoginVO（userId, token, openid）
> - JWT 密钥和 TTL 从配置文件读取，不可硬编码

## Step 3：登录拦截器

> 写 JwtTokenUserInterceptor：
> - 从请求头 Authorization 取 token
> - 调用 JwtUtil 解析拿到 userId
> - 写入 ThreadLocal（BaseContext.setCurrentId）
> - 注册到 WebMvcConfigurer，拦截路径 `/api/user/**`
> - 排除：/api/user/login、/api/user/send-code

## 关键 Review 点
- JWT 密钥不能硬编码
- Token 过期边界处理
- 验证码校验失败后 Redis 清理
- ThreadLocal 使用后清除（防止内存泄漏）
