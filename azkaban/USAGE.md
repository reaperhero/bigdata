# Azkaban 使用教程

## 基础操作流程

### 1. 创建项目

登录 UI → `Projects` → `Create Project` → 填写名称和描述。

### 2. 编写 Flow 与 Job

**job1.job**（Shell 脚本任务）：

```properties
type=command
command=echo "Job1 start" && sleep 5 && echo "Job1 end"
retries=2
```

**job2.job**（依赖 job1）：

```properties
type=command
command=echo "Job2 start"
dependencies=job1
```

### 3. 打包并上传

```bash
zip my_project.zip *.job
```

在项目页面点击 `Upload` → 选择 `my_project.zip`，系统自动解析并展示 Flow 图。

### 4. 执行 Flow

- **手动执行**：点击 Flow 名称 → `Execute Flow` → 观察实时日志
- **定时调度**：在 Flow 配置中填写 Cron 表达式（如 `0 0/30 * * * ?`）

### 5. REST API 调度

```bash
curl -X POST "http://<host>:8081/executor?ajax=executeFlow&project=myproj&flow=myflow"
```

## 高级功能

| 功能 | 说明 |
|------|------|
| SLA 告警 | 在 Flow 中配置超时阈值，超时后发送邮件 |
| 插件扩展 | 将自定义 Executor Plugin jar 放入 `plugins/` 目录 |
| 作业重试 | 在 `.job` 中设置 `retries=3` 和 `failureAction=finishAll` |

## 常见问题

| 场景 | 解决方案 |
|------|----------|
| Flow 卡在 RUNNING | 检查 Executor 心跳日志，确认网络连通性 |
| SLA 告警未触发 | 检查 SMTP 配置和 `executor.sla.email` 设置 |
| 上传后 Flow 不显示 | 确认 ZIP 根目录包含 `.job` 文件 |
| Executor 启动失败 | 检查端口占用和 `JAVA_HOME` 路径 |
