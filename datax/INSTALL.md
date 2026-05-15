# DataX 安装教程

## 前置条件

- JDK 1.8+（设置 `JAVA_HOME`）
- Python 2.7 或 3.x（启动脚本依赖）

## 安装步骤

### 方式一：下载预编译包（推荐）

```bash
# 下载
wget http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz

# 解压
tar -zxvf datax.tar.gz -C /opt/

# 验证
cd /opt/datax
python bin/datax.py job/job.json
```

运行自带的测试 Job（从 StreamReader 读数据到 StreamWriter），如果看到同步成功信息则安装正确。

### 方式二：源码编译

```bash
git clone https://github.com/alibaba/DataX.git
cd DataX
mvn -U clean package -DskipTests assembly:assembly
```

编译产物位于 `target/datax/datax/` 目录。

## 目录结构

```
datax/
├── bin/          # 启动脚本 datax.py
├── conf/         # 全局配置（core.json）
├── job/          # 示例 Job 配置
├── lib/          # 框架 jar 包
├── plugin/       # Reader/Writer 插件目录
│   ├── reader/
│   └── writer/
└── log/          # 运行日志
```

## 注意事项

- 同步关系型数据库时，需将对应 JDBC 驱动放入 `plugin/reader/<reader>/libs/` 目录
- `core.json` 中可配置全局 channel 数、限速等参数
- 生产环境建议配合 Azkaban/Airflow 等调度系统使用
