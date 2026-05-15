# Apache HBase 使用教程

## HBase Shell 操作

```bash
bin/hbase shell
```

### 表操作

```ruby
# 创建表
create 'users', 'info', 'contact'

# 查看表列表
list

# 查看表结构
describe 'users'
```

### 数据操作

```ruby
# 插入数据
put 'users', 'row1', 'info:name', 'Alice'
put 'users', 'row1', 'info:age', '30'
put 'users', 'row1', 'contact:email', 'alice@example.com'

# 查询单行
get 'users', 'row1'

# 扫描表
scan 'users'
scan 'users', {LIMIT => 10, FILTER => "SingleColumnValueFilter('info','age',>=,'binary:25')"}

# 删除数据
delete 'users', 'row1', 'contact:email'
deleteall 'users', 'row1'
```

### 表管理

```ruby
# 禁用表
disable 'users'

# 删除表（需先禁用）
drop 'users'
```

## Java API 示例

```java
Connection conn = ConnectionFactory.createConnection(conf);
Table table = conn.getTable(TableName.valueOf("users"));

// Put
Put put = new Put(Bytes.toBytes("row1"));
put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes("Bob"));
table.put(put);

// Get
Get get = new Get(Bytes.toBytes("row1"));
Result result = table.get(get);
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Region 不均衡 | 执行 `balancer` 命令或手动 `move` Region |
| RegionServer 频繁 GC | 增大堆内存，调整 BlockCache 和 MemStore 比例 |
| 写入性能差 | 使用批量 Put，关闭 WAL（非关键数据）|
