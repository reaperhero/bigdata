# Apache Oozie 安装教程

## 前置条件

- Hadoop 集群已运行
- 数据库（MySQL/Derby）用于存储作业状态
- ExtJS 库（用于 Web Console）

## 安装步骤

### 1. 下载并解压

```bash
tar -xzvf oozie-5.x.x-distro.tar.gz -C /opt/
cd /opt/oozie-5.x.x
```

### 2. 配置数据库

编辑 `conf/oozie-site.xml`：

```xml
<property>
  <name>oozie.service.JPAService.jdbc.driver</name>
  <value>com.mysql.cj.jdbc.Driver</value>
</property>
<property>
  <name>oozie.service.JPAService.jdbc.url</name>
  <value>jdbc:mysql://localhost:3306/oozie</value>
</property>
```

将 MySQL JDBC 驱动放入 `lib/` 目录。

### 3. 配置 Hadoop 代理用户

在 Hadoop 的 `core-site.xml` 中：

```xml
<property>
  <name>hadoop.proxyuser.oozie.hosts</name>
  <value>*</value>
</property>
<property>
  <name>hadoop.proxyuser.oozie.groups</name>
  <value>*</value>
</property>
```

### 4. 初始化并启动

```bash
bin/ooziedb.sh create -sqlfile oozie.sql -run
bin/oozie-setup.sh sharelib create -fs hdfs://namenode:8020
bin/oozied.sh start
```

### 5. 验证

```bash
bin/oozie admin -oozie http://localhost:11000/oozie -status
```
