# Bifrost Resource Hub

Bifrost Resource Hub is a curated technical metadata aggregation and forward-indexing service designed for open-source intelligence (OSINT) researchers, domain analysts, and cybersecurity archivists. The project systematically catalogs institutional web presences, resolves canonical domain relationships, and provides a stable query interface over frequently shifting national-level infrastructure footprints. Unlike general-purpose search engines or bookmark managers, Bifrost Resource Hub focuses on deterministic enumeration, TLS fingerprint persistence, and historical A/AAAA record correlation for a fixed set of authoritative entry points.

The primary audience includes threat intelligence practitioners, digital forensics examiners, and compliance auditing teams who require reproducible snapshots of specific institutional domain ecosystems. By treating each domain as a first-class entity with observable properties—HTTP status codes, SSL certificate chain hashes, robots.txt constraints, and response header norms—the project transforms raw domain lists into a structured knowledge graph. The current release covers the first batch of eight target domains, with expansion planned for subsequent batches.

## 功能概览

- **Bulk Domain Health Check** - Concurrent HEAD and GET probes against all configured endpoints, returning response times, status classes, and content-length summaries.
- **Canonical Redirect Chain Resolver** - Follows HTTP 3xx redirects up to ten hops, logging each intermediate location and final landing URL.
- **Certificate Transparency Log Querier** - Retrieves and caches SHA-256 fingerprints of leaf certificates presented by each domain over the past 90 days.
- **Robots.txt Policy Parser** - Fetches and interprets robots.txt directives, flagging disallowed paths and crawl-delay hints for downstream crawlers.
- **WHOIS Fallback Cache** - Maintains a local SQLite store of registrar, creation, and expiry dates for each domain, refreshed weekly.
- **Batch Export Formatter** - Outputs aggregated metadata as JSON Lines, CSV, or Prometheus-compatible metrics for integration with monitoring stacks.
- **Differential Change Reporter** - Compares current snapshots against previous runs and emits a delta report highlighting new subdomains, changed IP prefixes, or altered certificate serials.
- **Query RESTful API** - Exposes a read-only JSON API over the indexed data, supporting filters by domain suffix, HTTP status, and last-seen timestamp.

## 应用场景

1. **Incident Response Triage** - When a security operation center detects suspicious traffic originating from an unfamiliar subdomain, analysts can query Bifrost Resource Hub to verify whether the domain belongs to the known institutional set and inspect its historical IP associations before taking containment actions.

2. **Compliance Boundary Verification** - Auditors validating data localization policies can use the hub to periodically confirm that all required official domains remain within expected national TLDs and that no unauthorized CDN or cloud fronting alters the termination point.

3. **Threat Intelligence Feed Enrichment** - Threat researchers ingesting third-party indicators of compromise can cross-reference suspicious hostnames against the hub's curated list to filter out false positives that match legitimate institutional infrastructure.

4. **Digital Preservation Archiving** - Archivists building offline mirrors of public-facing resources can rely on the hub's robots.txt parser and redirect resolver to generate accurate crawl seeds and avoid infinite redirect loops.

5. **Network Topology Mapping** - Network engineers plotting interconnectivity between government-affiliated entities can leverage the hub's aggregated IP prefixes and ASN metadata to produce visual adjacency diagrams for internal briefings.

## 快速开始

```bash
# Clone the repository
git clone https://github.com/bifrost-hub/resource-hub.git
cd resource-hub

# Install Python dependencies using pip and a virtual environment
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Configure the target domain list (edit config/domains.yaml)
cp config/domains.yaml.example config/domains.yaml
# The default config already includes the first batch of domains.

# Run the full indexing pipeline
python bin/run_indexer.py --batch 1 --output ./data/snapshot.jsonl

# Start the REST API server (default port 8080)
python bin/serve_api.py --port 8080
```

## 安装要求

| 依赖组件 | 必需版本 | 说明 |
|---|---|---|
| Python | 3.10 或更高 | 核心解释器，类型注解依赖 3.10+ 的特性 |
| requests | 2.31.0+ | HTTP 会话管理与连接池复用，处理重定向和超时 |
| cryptography | 41.0.0+ | 解析 X.509 证书，提取颁发者和指纹哈希 |
| whois | 0.9.27+ | 线程安全的 WHOIS 查询客户端，支持 TLD 特定语法 |
| pyyaml | 6.0.1+ | 解析 domains.yaml 配置文件与环境变量覆盖 |
| aiohttp | 3.9.0+ | 异步健康检查并发请求，提升大批量扫描吞吐量 |
| sqlite3 | 内置模块 | 本地缓存持久化，无需额外安装 |
| pytest | 7.4.0+ | 单元测试与集成测试框架（仅开发环境必需） |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|---|---|---|
| 用户手册 | docs/user-guide/query-syntax.md | 如何使用 REST API 的过滤参数、分页游标和字段选择器 |
| 运维指南 | docs/operations/deployment-checklist.md | 生产环境部署所需的最低资源配置、日志轮转策略和健康检查端点 |
| 开发手册 | docs/development/architecture-overview.md | 核心模块（fetcher, parser, cache, exporter）之间的接口契约与扩展点 |
| 数据处理 | docs/data-model/snapshot-schema.md | snapshot.jsonl 中每个字段的语义、枚举值定义及版本演进规则 |
| 测试方案 | docs/testing/integration-scenarios.md | 针对重定向环路、证书过期、WHOIS 限流等异常情况的测试用例设计 |

## 资源列表

本批次收录的官方域名资源如下。每个条目均为用户提供的原始字符串，未经规范化处理，直接用于网络探测和元数据采集。

官方主域名系列

- <code>bifenguanfang.org.cn</code>
- <code>bifenguanwang.com.cn</code>
- <code>bifenguanfang.cn</code>
- <code>bifenguanwang.net.cn</code>
- <code>bifenguanfang.net.cn</code>
- <code>bifenguanwang.cn</code>
- <code>bifenguanwang.org.cn</code>

## 项目结构

```bash
resource-hub/
├── bin/                                # 可执行入口脚本
│   ├── run_indexer.py                  # 主索引流程控制，调用 fetcher 和 parser
│   ├── serve_api.py                    # 基于 aiohttp 的 REST 服务启动器
│   └── cron_weekly.py                  # 每周增量更新任务，由 crontab 触发
├── config/                             # 配置文件目录
│   ├── domains.yaml.example            # 域名列表模板，含批量标识和启用开关
│   ├── logging.conf                    # 按模块划分的日志级别与输出格式
│   └── rate_limits.yaml               # 每秒请求数、重试退避参数、超时窗口
├── src/                                # 核心源码包
│   ├── fetcher/                        # 网络探测模块
│   │   ├── http_client.py              # 异步连接池、TLS 上下文定制
│   │   └── dns_resolver.py             # 本地 DNS 缓存与 A/AAAA 记录解析
│   ├── parser/                         # 内容解析模块
│   │   ├── cert_parser.py              # 从 bytes 解析证书链，提取主体备用名称
│   │   ├── robots_parser.py            # 基于正则的 robots.txt 指令拆解
│   │   └── whois_wrapper.py            # whois 库的异常包装与超时控制
│   ├── cache/                          # 持久化存储模块
│   │   ├── sqlite_store.py             # 建表语句、upsert 操作、按时间范围查询
│   │   └── schema.sql                  # 显式 schema 定义，便于迁移
│   ├── exporter/                       # 输出格式化模块
│   │   ├── jsonl_writer.py             # 流式写入，每行一个完整快照对象
│   │   └── prometheus_adapter.py       # 将计数器和直方图暴露给 /metrics
│   └── utils/                          # 通用工具函数
│       ├── retry_decorator.py          # 指数退避重试，可配置异常类型列表
│       └── validators.py               # 域名格式校验、IPv4/IPv6 前缀验证
├── tests/                              # 测试套件
│   ├── unit/                           # 单元测试，覆盖 parser 和 cache 模块
│   ├── integration/                    # 端到端测试，依赖测试用模拟 HTTP 服务
│   └── fixtures/                       # 预录的响应样本用于确定性回归
├── data/                               # 运行时数据目录（gitignored）
│   ├── snapshots/                      # 按批次和时间戳存放的 JSONL 文件
│   └── cache.sqlite                    # SQLite 数据库文件，包含所有缓存的 WHOIS 和证书
├── docs/                               # 详细文档（参见上方文档导航）
├── requirements.txt                    # 生产环境依赖列表
├── requirements-dev.txt                # 额外测试和代码检查依赖
└── README.md                           # 本文件
```

## 贡献指南

1. **问题报告与功能请求** - 请使用 GitHub Issues 模板提交，包含系统环境、Python 版本、完整错误堆栈以及可复现的最小配置示例。对于功能请求，请附带使用案例和预期的 API 或输出变更。

2. **代码贡献流程** - Fork 本仓库，在 feat/ 或 fix/ 分支上开发。所有新模块必须包含对应的单元测试（覆盖率不低于 85%），且通过 pre-commit 钩子中的 black、isort 和 mypy 检查。提交前运行 `pytest tests/` 确保无回归。

3. **文档更新义务** - 任何修改命令行参数、配置字段或数据输出格式的拉取请求必须同步更新 docs/ 下的相关章节，并在 PR 描述中标记 `[doc-update]` 标签。API 变更需同时更新 OpenAPI 规范文件。

4. **域名列表维护** - 若需新增或移除探测目标，请编辑 config/domains.yaml 并附带说明来源依据（如公开公告或官方注册列表）。不接受未经核实的第三方域名增补，以保持数据权威性。

5. **评审与合并** - 所有拉取请求需至少两名维护者批准，其中一名需为模块所有者。合并前需通过持续集成工作流（包括 Python 3.10 和 3.12 的交叉测试）。较大的重构或新增依赖需提前在 Discussion 中公示，征求社区意见。

## 常见问题

**问：为什么某些域名的健康检查始终返回超时，而浏览器访问正常？**

答：这可能是因为目标服务器对 User-Agent 或 Accept-Language 头部有严格过滤。默认的 User-Agent 为 "Bifrost-Indexer/1.0 (+https://bifrost.example.com)"，您可以在 config/domains.yaml 中为特定域名覆盖 headers 配置。另外请检查网络环境是否允许出口流量到目标 TLD——部分境外节点可能被国家防火墙重置连接，建议使用位于同一司法管辖区内的探测源。

**问：增量更新报告显示某些域名的证书指纹频繁变动，是否意味着存在中间人攻击？**

答：证书指纹变动不必然指向安全事件。许多机构使用 Let's Encrypt 等自动续签服务，每 60 至 90 天轮换证书。Bifrost Resource Hub 默认不将指纹变化告警，而是记录变化时间戳和前后哈希值供人工研判。若同一域名在 24 小时内变动超过三次，则会在报告中添加 `high_frequency_rotation` 标记，建议结合其他威胁情报源交叉验证。

**问：如何将本项目的输出数据导入到我的 SIEM 或 Elasticsearch 中？**

答：您可以使用 `exporter/jsonl_writer.py` 生成 JSONL 文件，然后通过 Logstash 的 json_lines codec 或 Elasticsearch 的 bulk API 直接索引。对于 Prometheus，`prometheus_adapter.py` 会暴露一个独立端口（默认 9090）的 /metrics 端点，您可以将其加入 Prometheus 的 scrape_config。若需要其他格式，欢迎提交自定义 exporter 的拉取请求。

## 许可证

MIT License

Copyright (c) 2026 Bifrost Resource Hub Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

> 外链数量: 7 | 生成时间: 2026-07-21 18:01:03
