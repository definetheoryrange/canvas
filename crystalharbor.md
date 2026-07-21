# Bifrost Gateway Index

Bifrost Gateway Index 是一个面向基础设施运维、网络边界安全研究及企业信息化审计领域的开源外链资源聚合与导航系统。本项目不提供具体业务服务，而是作为技术文献、官方门户与合规性参考信息的结构化索引层，帮助技术决策者、安全审计人员及网络架构师快速定位多源异构的官方入口与备案信息页面。

项目的目标用户包括企业内网运维团队、第三方安全评估机构、跨境网络合规专员以及基础设施即代码（IaC）策略制定者。通过集中管理分散在多个顶级域名下的官方门户入口，Bifrost Gateway Index 解决了因域名分治、备案主体不一致、证书策略差异导致的资源发现延迟与审计溯源困难问题。本项目本身不存储任何敏感数据，仅维护公开可访问的 URL 索引及其元信息标签。

## 功能概览

- **多源入口聚合管理**：支持将分散在不同注册局、不同 DNS 服务商下的官方门户入口纳入统一索引空间，提供单一入口检索视图。

- **域名状态快照记录**：对每个收录的 URL 记录其协议类型、服务器响应头部特征及 TLS 证书颁发者信息，便于日常健康检查。

- **合规备案信息关联**：支持将域名与对应的 ICP 备案号、公安备案号及企业工商注册号建立软关联，满足审计追踪要求。

- **变更历史追踪**：内置基于 Git 的元数据版本管理，每次收录的增删改操作均生成提交记录，支持回溯任意时间点的索引状态。

- **外部检查触发接口**：提供 RESTful API 用于触发外部监控系统对收录 URL 进行连通性测试与内容指纹比对，返回结构化 JSON 报告。

- **标签化分类体系**：允许用户为每个 URL 打上自定义标签（如 "生产环境"、"测试环境"、"备用入口"），并支持按标签组合过滤。

- **只读只写权限分离**：面向生产环境提供只读查询接口，面向管理员提供写入接口，内置基于 Token 的简单鉴权机制。

- **批量导入导出**：支持 CSV 与 YAML 格式的批量导入导出，便于与其他资产管理系统对接或进行离线审计。

## 应用场景

1. **企业年度合规自查**  
   合规部门每季度需要核对所有对外服务入口的域名是否与备案信息一致。使用本项目可快速导出当前收录的所有 URL 列表，并与备案数据库做交叉比对，生成差异报告。

2. **安全边界变更评估**  
   当安全团队计划调整防火墙策略或 WAF 规则时，需先了解所有官方入口的 IP 地址段与端口开放情况。本项目提供的索引数据可作为变更影响分析的输入清单。

3. **灾备切换演练**  
   运维团队在执行灾备切换前，需确认备用入口域名的解析状态与证书有效性。通过本系统集中查看备用域名的快照信息，可提前发现证书过期或 DNS 解析异常问题。

4. **第三方供应商接入审核**  
   在与外部合作伙伴进行系统对接时，需验证对方提供的回调域名是否属于官方注册范围。本项目可作为辅助验证工具，快速查找域名的注册主体与备案信息。

5. **内部知识库构建**  
   技术文档团队可将本项目作为数据源，自动生成官方入口列表并嵌入内部 Wiki，减少人工维护表格的重复劳动与出错风险。

## 快速开始

以下步骤适用于 Linux 及 macOS 环境，Windows 用户建议使用 WSL2 或 Git Bash。

```bash
# 克隆项目仓库
git clone https://github.com/bifrost-index/gateway.git
cd gateway

# 安装依赖（使用 Python 3.9+ 与 pip）
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 初始化本地数据库并导入示例索引数据
python manage.py migrate
python manage.py loaddata initial_index.json

# 启动开发服务器（默认监听 127.0.0.1:8000）
python manage.py runserver
```

访问 http://127.0.0.1:8000/api/v1/entries/ 即可查看已收录的 URL 列表。生产环境部署请参考 `deploy/production.md` 文档。

## 安装要求

| 依赖组件 | 必需版本 | 说明 |
|---------|---------|------|
| Python | 3.9, 3.10, 3.11 | 核心运行环境，不支持 3.8 以下版本 |
| Django | 4.2.7 LTS | Web 框架及 ORM 层，用于数据建模与 API 视图 |
| djangorestframework | 3.14.0 | 提供 RESTful 序列化与视图集，用于 API 接口暴露 |
| psycopg2-binary | 2.9.9 | PostgreSQL 数据库驱动，生产环境推荐使用 PostgreSQL 15+ |
| redis-py | 5.0.1 | 可选依赖，用于 API 响应缓存与限流计数器 |
| PyYAML | 6.0.1 | 用于 YAML 格式的批量导入导出功能 |
| cryptography | 41.0.7 | 用于 TLS 证书信息的解析与指纹提取 |
| git | 2.30+ | 用于版本变更历史的底层存储，需确保 `git` 命令在 PATH 中 |
| curl | 7.68+ | 用于外部检查触发时的 HTTP 探测，需支持 HTTPS 及重定向跟踪 |

## 文档导航

| 层面 | 目录 | 回答的问题 |
|------|------|-----------|
| 用户手册 | `docs/user-guide/` | 如何查询收录条目、如何添加自定义标签、如何导出过滤后的 URL 列表 |
| 管理员手册 | `docs/admin-guide/` | 如何配置鉴权 Token、如何执行批量导入、如何调整缓存策略与限流阈值 |
| 开发者手册 | `docs/developer-guide/` | 如何扩展自定义检查器、如何增加新的元数据字段、如何编写迁移脚本 |
| 运维部署 | `docs/deployment/` | 如何配置 uWSGI + Nginx 生产环境、如何设置 PostgreSQL 主从同步、如何配置日志轮转 |
| API 参考 | `docs/api-reference/` | 所有端点路径、请求参数格式、响应结构示例、错误码含义说明 |
| 变更日志 | `CHANGELOG.md` | 每个版本的特性新增、缺陷修复、不兼容变更说明及升级注意事项 |

## 资源列表

### 主要官方入口

<code>bifenguanfang.org.cn</code>

<code>bifenguanwang.com.cn</code>

<code>bifenguanfang.cn</code>

### 备用网络入口

<code>bifenguanwang.net.cn</code>

<code>bifenguanfang.net.cn</code>

### 补充注册域名

<code>bifenguanwang.cn</code>

<code>bifenguanwang.org.cn</code>

## 项目结构

```
gateway/
├── index/                           # 主应用目录
│   ├── __init__.py
│   ├── settings.py                  # 项目配置（数据库、缓存、时区、中间件）
│   ├── urls.py                      # 根路由分发，注册 API 及管理后台
│   ├── wsgi.py                      # 生产环境 WSGI 入口
│   ├── asgi.py                      # 异步 ASGI 入口（预留）
│   ├── models/                      # 数据模型层
│   │   ├── __init__.py
│   │   ├── entry.py                 # Entry 模型：存储 URL、标题、描述、标签
│   │   ├── snapshot.py              # Snapshot 模型：存储每次检查的响应码、证书指纹
│   │   └── audit.py                 # AuditLog 模型：记录所有变更操作
│   ├── views/                       # 视图与视图集
│   │   ├── __init__.py
│   │   ├── api_v1.py                # REST API 端点：查询、创建、更新、删除
│   │   └── admin.py                 # Django admin 后台定制
│   ├── serializers/                 # 序列化器（请求/响应结构定义）
│   ├── management/                  # 自定义管理命令
│   │   └── commands/
│   │       ├── import_urls.py       # 从 CSV/YAML 导入 URL 列表
│   │       └── check_all.py         # 触发对所有收录 URL 的连通性检查
│   ├── utils/                       # 工具函数
│   │   ├── cert_parser.py           # TLS 证书解析与指纹计算
│   │   └── http_probe.py            # HTTP/HTTPS 探测与重定向跟踪
│   └── migrations/                  # 数据库迁移脚本（自动生成）
├── tests/                           # 单元测试与集成测试
│   ├── test_models.py
│   ├── test_api.py
│   └── test_probe.py
├── scripts/                         # 辅助运维脚本
│   ├── backup_index.sh              # 每日索引备份脚本
│   └── sync_to_remote.sh            # 同步数据到只读副本
├── docs/                            # 详细文档（参考上方文档导航）
├── deploy/                          # 部署相关配置
│   ├── nginx.conf                   # Nginx 站点配置模板
│   ├── uwsgi.ini                    # uWSGI 进程管理配置
│   └── supervisor.conf              # Supervisor 进程守护配置
├── requirements.txt                 # 生产环境依赖列表
├── requirements-dev.txt             # 开发与测试额外依赖
├── manage.py                        # Django 命令行管理入口
├── README.md                        # 本文件
├── CHANGELOG.md                     # 版本变更记录
└── LICENSE                          # MIT 许可证文本
```

## 贡献指南

1. 复刻（Fork）本仓库至个人账号，并克隆到本地开发环境。确保本地 Python 版本符合要求，且已安装所有开发依赖（使用 `requirements-dev.txt`）。

2. 新建功能分支，分支名称应遵循 `feature/简述` 或 `fix/简述` 格式，例如 `feature/add-ipv6-check`。禁止在主分支上直接修改。

3. 编写代码或文档变更时，请同步更新对应的单元测试用例，确保测试覆盖率不低于 85%。新增 API 端点时必须编写集成测试用例。

4. 提交前执行完整的测试套件：`python manage.py test`，并确保代码风格通过 `flake8` 与 `black --check` 检查。如有 lint 错误，请修正后再提交。

5. 发起 Pull Request 至主仓库的 `develop` 分支，PR 描述中需明确说明变更目的、影响范围及测试结果。PR 至少需要一名维护者审核通过方可合并。

## 常见问题

**Q：收录的域名如果变更了 IP 地址或证书，系统如何感知？**

A：系统本身不主动轮询外部地址。管理员需定期执行 `python manage.py check_all` 命令，或通过外部定时任务调用 API 触发检查。检查结果会更新到 Snapshot 表中，并可通过管理后台查看变化趋势。若需自动化监控，建议搭配 Prometheus 或 Zabbix 使用自定义 exporter。

**Q：如何保证收录 URL 的合规性，避免收录已注销或违规域名？**

A：本项目仅作为技术索引工具，不承担内容审核责任。管理员在新增条目时应参考国家工信部备案查询系统及 WHOIS 信息进行人工核验。项目提供 `--verify` 命令行选项，可调用第三方备案查询 API 进行辅助验证，但最终责任由录入方承担。

**Q：生产环境下 API 的限流策略如何配置？**

A：在 `settings.py` 中通过 `REST_FRAMEWORK` 字典配置 `DEFAULT_THROTTLE_RATES`，支持按用户或按 IP 分别设置 `anon` 和 `user` 的请求频率。默认值为每分钟 60 次匿名请求和每分钟 600 次认证请求。如需更细粒度的控制，可扩展自定义限流类。

## 许可证

MIT License

Copyright (c) 2026 Bifrost Gateway Index Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

> 外链数量: 7 | 生成时间: 2026-07-21 18:01:03
