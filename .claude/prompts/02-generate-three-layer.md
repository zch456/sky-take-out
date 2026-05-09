# Prompt: 生成三层代码

## 使用场景
每个业务模块的标准 CRUD 代码生成（Controller / Service / Mapper / DTO）

## Prompt

> 基于 {表名} 表生成完整三层代码：
> 
> **Controller 层：**
> - RESTful 风格，@Slf4j 日志，方法入参加 @Validated 校验
> - 返回统一响应体 `Result<T>`
> - 分页查询入参为 `{Entity}PageQueryDTO`（page, pageSize, name 模糊搜索），返回 `PageResult<T>`
> 
> **Service 层：**
> - 接口 + 实现类
> - 业务异常统一抛出自定义异常（sky-common/exception/）
> - 需要事务的方法加 @Transactional
> 
> **Mapper 层：**
> - 继承 MyBatis-Plus BaseMapper
> - 简单查询用注解，复杂查询写 XML
> 
> **DTO：**
> - `{Entity}DTO`：新增/修改入参
> - `{Entity}PageQueryDTO`：分页查询入参
> 
> 约束：
> - 遵循阿里巴巴 Java 开发规范
> - 时间字段用 LocalDateTime
> - 统一使用 com.sky.result.Result 和 com.sky.result.PageResult

## 复用情况
此模板复用于：菜品管理、套餐管理、分类管理、员工管理、地址簿等模块。
同类模块开发效率提升约 60%。
