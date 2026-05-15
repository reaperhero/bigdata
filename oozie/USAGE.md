# Apache Oozie 使用教程

## 编写 Workflow

### workflow.xml

```xml
<workflow-app name="my-workflow" xmlns="uri:oozie:workflow:0.5">
    <start to="hive-action"/>

    <action name="hive-action">
        <hive xmlns="uri:oozie:hive-action:0.5">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <script>my_query.hql</script>
        </hive>
        <ok to="end"/>
        <error to="fail"/>
    </action>

    <kill name="fail">
        <message>Workflow failed</message>
    </kill>
    <end name="end"/>
</workflow-app>
```

### job.properties

```properties
nameNode=hdfs://namenode:8020
jobTracker=resourcemanager:8032
oozie.wf.application.path=${nameNode}/user/oozie/workflows/my-workflow
```

## 提交作业

```bash
# 上传到 HDFS
hdfs dfs -put workflow.xml /user/oozie/workflows/my-workflow/

# 提交运行
oozie job -oozie http://localhost:11000/oozie -config job.properties -run

# 查看状态
oozie job -oozie http://localhost:11000/oozie -info <job-id>
```

## 定时调度（Coordinator）

```xml
<coordinator-app name="my-coord" frequency="${coord:days(1)}" start="2024-01-01T00:00Z" end="2024-12-31T00:00Z" xmlns="uri:oozie:coordinator:0.4">
    <action>
        <workflow>
            <app-path>${nameNode}/user/oozie/workflows/my-workflow</app-path>
        </workflow>
    </action>
</coordinator-app>
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| Action 失败 | 查看 Oozie Web Console 中的 Action 日志 |
| ShareLib 找不到 | 运行 `oozie-setup.sh sharelib create` 重新部署 |
| 代理用户权限错误 | 检查 `core-site.xml` 中的 `hadoop.proxyuser.oozie` 配置 |
