# Apache Atlas 使用教程

## 配置 Hook 自动采集元数据

Atlas 通过 Hook 机制自动从各大数据组件采集元数据。以 Hive 为例：

1. 将 Atlas Hive Hook jar 包复制到 Hive 的 `lib/` 目录
2. 在 `hive-site.xml` 中添加：

   ```xml
   <property>
     <name>hive.exec.post.hooks</name>
     <value>org.apache.atlas.hive.hook.HiveHook</value>
   </property>
   ```

3. 重启 HiveServer2。之后所有 Hive DDL/DML 操作都会自动同步到 Atlas。

类似地，Spark、Sqoop、Storm 等组件也有对应的 Hook 可配置。

## 通过 Web UI 浏览元数据

1. 登录 `http://<host>:21000`
2. 在搜索栏输入关键词（表名、列名、标签等），支持模糊搜索和 DSL 语法
3. 点击搜索结果中的实体，可查看：
   - **属性（Properties）**：表名、列定义、创建时间等
   - **血缘（Lineage）**：上下游数据流向图
   - **分类（Classifications）**：已打的标签（如 PII、Sensitive）
   - **审计（Audit）**：实体的变更历史

## 使用 REST API

### 创建实体

```bash
curl -X POST -u admin:admin \
  -H "Content-Type: application/json" \
  http://localhost:21000/api/atlas/v2/entity \
  -d '{
    "entity": {
      "typeName": "hive_table",
      "attributes": {
        "qualifiedName": "default.my_table@primary",
        "name": "my_table",
        "db": { "typeName": "hive_db", "uniqueAttributes": { "qualifiedName": "default@primary" } }
      }
    }
  }'
```

### 搜索实体

```bash
# 基础搜索
curl -u admin:admin "http://localhost:21000/api/atlas/v2/search/basic?query=my_table"

# DSL 搜索
curl -u admin:admin "http://localhost:21000/api/atlas/v2/search/dsl?query=hive_table%20where%20name%3D'my_table'"
```

### 添加分类标签

```bash
curl -X POST -u admin:admin \
  -H "Content-Type: application/json" \
  http://localhost:21000/api/atlas/v2/entity/guid/<GUID>/classifications \
  -d '[{"typeName": "PII"}]'
```

## 结合 Ranger 实现标签安全

1. 在 Atlas 中为敏感表/列打上分类标签（如 `PII`、`SENSITIVE`）
2. 在 Ranger Admin 中创建 Tag-Based Policy：
   - 选择标签 `PII`
   - 配置允许/拒绝的用户和操作
3. Ranger Plugin 会自动从 Atlas 同步标签信息，对匹配的数据资产执行访问控制

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| 登录后页面空白 | 检查 Solr 是否正常运行，Atlas 依赖 Solr 提供搜索能力 |
| Hive Hook 未生效 | 确认 Hook jar 在 Hive classpath 中，且 `hive-site.xml` 配置正确；查看 HiveServer2 日志 |
| 血缘图不显示 | 需要实际执行过含数据流转的 HiveQL（如 `CREATE TABLE AS SELECT`）才会产生血缘 |
| REST API 返回 401 | 检查用户名密码是否正确，生产环境可能启用了 Kerberos 认证 |
