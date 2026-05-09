# Prompt: RBAC 权限管理

## 使用阶段
项目第 19-22 天，后台管理端

## Prompt

> 后台 RBAC 权限体系：
> 
> **模型：账号 → 角色 → 菜单（三级权限）**
> 
> **权限拦截器：**
> - 自定义注解 @AdminCheck
> - AdminInterceptor 从 ThreadLocal 取 userId，查数据库 user 表获取 role
> - role != "ADMIN" 返回 Result(403, "无管理员权限")
> - 注册到 WebMvcConfigurer，拦截路径 `/api/admin/**`
> 
> **后台管理接口：**
> - 员工管理：分页列表 / 新增 / 编辑 / 启用-禁用（@AdminCheck）
> - 菜品管理：分页 + 条件筛选 / 新增（含图片上传 OSS）/ 修改 / 批量起售-停售 / 批量删除
> - 套餐管理：同菜品管理 + 关联菜品选择
> - 订单管理：分页 + 状态筛选 / 接单 / 拒单 / 派送 / 完成
> - 数据统计：今日订单概览 / 营业额统计 / 销量 Top10
> 
> **约束：**
> - 文件上传到阿里云 OSS，返回可访问 URL 再写入数据库
> - Excel 导出使用 Apache POI（数据统计模块）
> - 接口文档使用 Knife4j（Swagger 增强）注解

## 说明
后台管理端的 CRUD 代码几乎全部使用 `02-generate-three-layer` 模板生成，Prompt 模板复用率最高。
