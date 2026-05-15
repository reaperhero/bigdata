# Apache YARN 安装教程

## 概述

YARN 是 Hadoop 发行包的一部分，与 HDFS 一同安装和部署。

## 配置

### yarn-site.xml

```xml
<configuration>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>rm-host</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.resource.memory-mb</name>
    <value>8192</value>
  </property>
  <property>
    <name>yarn.nodemanager.resource.cpu-vcores</name>
    <value>4</value>
  </property>
  <property>
    <name>yarn.scheduler.minimum-allocation-mb</name>
    <value>1024</value>
  </property>
  <property>
    <name>yarn.scheduler.maximum-allocation-mb</name>
    <value>8192</value>
  </property>
</configuration>
```

### mapred-site.xml

```xml
<property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
</property>
```

## 启动

```bash
start-yarn.sh
```

## 验证

- `jps` 确认 ResourceManager 和 NodeManager 进程
- Web UI：`http://rm-host:8088`

## ResourceManager HA 配置

在 `yarn-site.xml` 中配置：

```xml
<property>
  <name>yarn.resourcemanager.ha.enabled</name>
  <value>true</value>
</property>
<property>
  <name>yarn.resourcemanager.ha.rm-ids</name>
  <value>rm1,rm2</value>
</property>
```
