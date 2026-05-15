# Apache Ranger 使用教程

## 创建服务

1. 登录 Ranger Admin（`http://<host>:6080`）
2. 点击 `+` 添加 Service（如 Hive、HDFS）
3. 配置连接信息（JDBC URL、用户名等）
4. 测试连接 → 保存

## 创建访问策略

1. 进入对应 Service → 点击 `Add New Policy`
2. 配置：
   - **Resource**：指定数据库、表、列
   - **Allow Conditions**：允许的用户/组及操作（SELECT、INSERT 等）
   - **Deny Conditions**：拒绝的用户/组

### 示例：允许 analyst 组只读 sales 数据库

| 字段 | 值 |
|------|------|
| Database | sales |
| Table | * |
| Column | * |
| Group | analyst |
| Permissions | SELECT |

## 标签策略（Tag-Based Policy）

1. 在 Atlas 中为敏感表打标签（如 `PII`）
2. 在 Ranger 创建 Tag Service
3. 创建 Tag Policy：对标签 `PII` 限制访问或启用列脱敏

## 审计查看

在 Ranger Admin 的 `Audit` 标签页可查看：
- 谁在什么时间访问了什么数据
- 访问被允许还是拒绝
- 使用的策略 ID

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Plugin 未生效 | 确认 Plugin 安装后重启了对应组件 |
| 策略不生效 | 检查 Plugin 与 Ranger Admin 的连接，查看 Plugin 日志 |
| UserSync 不工作 | 检查 LDAP 连接配置和用户搜索 base DN |
