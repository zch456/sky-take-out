# Prompt: 项目初始化与模块拆分

## 使用阶段
项目启动第 1 天

## Prompt

> 我要开发一个面向校园场景的外卖点餐平台，技术栈如下：
> - 后端：Spring Boot 2.7.3 + MyBatis + MySQL 8.0 + Redis + JWT
> - 前端：微信小程序（UniApp）+ 后台管理端（Vue + Element UI）
> 
> 请帮我：
> 1. 设计项目包结构（多模块 Maven，分为 common / pojo / server 三个子模块）
> 2. 列出 pom.xml 的依赖清单（含版本号）
> 3. 创建 Spring Boot 主启动类和基础配置文件（application.yml、application-dev.yml）
> 4. 设计统一响应体 `Result<T>` 和分页结果 `PageResult<T>`
> 
> 约束：
> - 遵循阿里巴巴 Java 开发规范
> - 配置文件使用 yml 格式，dev/test/prod 三环境分离
> - 统一响应体包含 code、msg、data 三个字段

## 实际产出
- pom.xml（父子模块依赖管理）
- 项目包结构树
- Spring Boot 启动类
- Result<T> 和 PageResult<T>
- 三环境配置文件骨架

## 效果
传统方式需 2-3 天的工作量，约 4 小时内完成。
