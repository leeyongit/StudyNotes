## 什么是并发数、并发连接数、每秒请求数（PRS）、QPS，如何查看



以下是对并发数、并发连接数、每秒请求数（PRS）和 QPS 的定义，以及如何查看这些指标的方法。

### 1. 并发数
- **定义**：指在同一时间点上与服务器交互的用户数量。它表示在某一时刻同时发起请求的用户数。
- **查看方法**：
  - 使用压力测试工具（如 Apache Benchmark、JMeter）进行并发测试，记录并发用户数量。
  - 通过服务器监控工具（如 New Relic、Datadog）查看当前并发用户数量。

### 2. 并发连接数
- **定义**：指在同一时间点上与服务器建立的活动连接数量。这些连接通常是 TCP/IP 连接。
- **查看方法**：
  - 使用命令行工具查看活动连接：
    ```bash
    netstat -an | grep ESTABLISHED | wc -l
    ```
  - 使用服务器监控工具（如 Prometheus）监控并发连接数。

### 3. 每秒请求数（PRS）
- **定义**：指服务器在一秒钟内处理的请求数量。它反映了服务器的处理能力。
- **查看方法**：
  - 使用应用程序日志或 Web 服务器日志（如 Nginx、Apache）统计每秒请求数。
  - 通过监控工具（如 Grafana）配置请求统计仪表盘，实时查看每秒请求数。

### 4. QPS (Queries Per Second)
- **定义**：通常用于数据库，表示每秒查询的数量。QPS 是一种衡量数据库性能的指标。
- **查看方法**：
  - 使用数据库的性能监控工具（如 MySQL 的 `SHOW STATUS` 命令）来查看 QPS。
  - 配置数据库监控工具（如 Percona Monitoring and Management），实时查看 QPS。

### 总结
- **并发数**：实时活跃的用户数。
- **并发连接数**：实时活动的网络连接数。
- **每秒请求数（PRS）**：服务器每秒处理的请求数量。
- **QPS**：数据库每秒执行的查询数量。

使用适当的监控和测试工具，可以实时查看和分析这些指标，帮助优化应用性能和服务器响应能力。

