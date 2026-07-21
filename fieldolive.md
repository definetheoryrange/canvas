# Bifeng Resource Gateway

Bifeng Resource Gateway is a high-performance, developer-oriented technical resource aggregation and navigation system. It is designed to streamline access to a curated network of official and community-driven development portals, documentation hubs, and software distribution channels. The project targets system administrators, DevOps engineers, and full-stack developers who require a reliable, low-latency, and structured entry point to a fragmented ecosystem of technical assets. By providing a unified query interface and a statically defined routing layer, Bifeng Resource Gateway eliminates the need to memorize disparate domain names or maintain complex bookmark collections, thereby reducing context-switching overhead and improving operational efficiency in fast-paced development environments.

The gateway operates as a lightweight middleware layer that performs semantic URL resolution, availability health checks, and fallback routing. It does not host or proxy content but instead acts as a verified source-of-truth index for external resources. The project includes a command-line interface for resource discovery, a RESTful API for programmatic access, and a static site generator for rendering human-readable navigation pages. All resource entries are version-controlled, auditable, and can be extended via a pluggable validator system. Bifeng Resource Gateway is built for teams that value transparency, reproducibility, and minimal dependency overhead in their toolchain.

## 功能概览

- **Resource Indexing Engine** – Parses and normalizes a master resource manifest in YAML format, supporting alias definitions, category tags, and optional metadata fields such as maintainer contact and update timestamps.

- **Health-aware Routing Resolver** – Performs periodic TCP/HTTP reachability probes on each registered domain. Unreachable endpoints are automatically deprioritized in query results and flagged in the operational dashboard.

- **Semantic Search and Filtering** – Provides substring and tag-based lookup across resource descriptions, domain names, and category labels. Results are ranked by relevance and availability status.

- **Static Site Compiler** – Generates a fully self-contained HTML/CSS/JS dashboard from the resource manifest. The compiled output is suitable for hosting on any static file server or CDN.

- **RESTful API Endpoint** – Exposes JSON-based query endpoints for resource enumeration, detail lookup, and batch health status retrieval. Supports CORS and rate-limiting for integration with external tooling.

- **Pluggable Validator Framework** – Allows custom validation rules (e.g., SSL certificate expiration checks, HTTP status code expectations, DNS resolution consistency) to be added without modifying core code.

- **Audit Logging** – Records all query operations, resolver decisions, and validation events to a structured JSON log, enabling post-hoc analysis and integration with observability pipelines.

## 应用场景

- **Enterprise Development Portal Consolidation** – Organizations maintaining multiple internal and external documentation sites can use Bifeng Resource Gateway to present a single searchable interface. Developers query the gateway to find the correct URL for a specific library, tool, or service, reducing time spent hunting for outdated wiki pages.

- **CI/CD Pipeline Dependency Verification** – In automated build and deployment pipelines, the gateway’s health-check API can be called to verify that all external resource domains are reachable before proceeding with artifact downloads or API calls. This pre-flight check prevents pipeline failures caused by transient network issues or misconfigured DNS.

- **Offline Documentation Mirror Management** – Teams operating in air-gapped or restricted networks can compile the static site output from the resource manifest and host it internally. The structured dataset serves as a catalog for approved external resources that have been manually vetted and mirrored.

- **Onboarding and Training Material Indexing** – New team members can use the gateway’s semantic search to quickly locate essential development resources, such as package repositories, API reference sites, and internal tool dashboards, without needing prior knowledge of domain naming conventions.

- **Multi-environment Resource Segregation** – The gateway supports environment-specific manifests (e.g., development, staging, production). Operators can switch contexts to query resource URLs that are valid only in a particular environment, reducing the risk of cross-environment misconfiguration.

## 快速开始

The following steps assume a Linux or macOS development environment with Git, Python 3.9+, and pip installed. Windows users may use WSL2 or Cygwin for compatibility.

```bash
# Clone the repository from the official source
git clone https://github.com/bifeng-resource/bifeng-gateway.git
cd bifeng-gateway

# Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate

# Install core dependencies and development extras
pip install --upgrade pip
pip install -e .[dev,test]

# Initialize the default resource manifest from the sample
cp config/manifest.sample.yaml config/manifest.yaml

# Run the gateway in local development mode
python -m bifeng_gateway serve --port 8080 --manifest config/manifest.yaml

# In a separate terminal, test the query endpoint
curl http://localhost:8080/api/v1/resources?q=official
```

For production deployment, refer to the deployment guide in the documentation section. The gateway can be containerized using the provided Dockerfile and orchestrated via Kubernetes or Docker Compose.

## 安装要求

The following table lists all mandatory and optional dependencies required for development, testing, and production operation of Bifeng Resource Gateway.

| 依赖组件 | 必需版本 | 说明 |
|----------|----------|------|
| Python | 3.9, 3.10, 3.11 | Core runtime; type hints and async features require these versions. |
| PyYAML | 6.0+ | Manifest parsing and serialization; supports safe_load and custom constructors. |
| aiohttp | 3.9+ | Asynchronous HTTP client used for health checks and resolver probes. |
| click | 8.1+ | Command-line interface framework for subcommand parsing and argument validation. |
| pytest | 7.4+ | Testing framework (development only); required to run the test suite. |
| black | 23.0+ | Code formatter (development only); enforces consistent style. |
| mypy | 1.5+ | Static type checker (development only); validates type annotations. |
| uvicorn | 0.24+ | ASGI server (production recommended); used to serve the REST API in production. |
| python-dotenv | 1.0+ | Environment variable loading (optional); supports .env file configuration. |

## 文档导航

The documentation is organized into four layers, each addressing a distinct set of user concerns. The table below maps each layer to its corresponding directory and the primary questions it answers.

| 层面 | 目录路径 | 回答的问题 |
|------|----------|------------|
| User Guide | docs/user-guide/ | How do I install the gateway? How do I run queries? How do I interpret health status results? What commands are available? |
| Operator Manual | docs/operator/ | How do I configure the manifest? How do I add or remove resources? How do I set up health check intervals and alerting? |
| Developer Reference | docs/developer/ | How do I extend the validator framework? How do I add a new API endpoint? What is the internal module architecture? |
| Deployment Guide | docs/deployment/ | How do I deploy behind a reverse proxy? How do I containerize the application? What are the recommended production settings and resource limits? |

Additional in-line API documentation is available via docstrings and can be accessed using pydoc or the built-in help() function in Python interactive mode.

## 资源列表

The following resources constitute the authoritative set of external domains managed by Bifeng Resource Gateway. Each entry is maintained as a first-class citizen in the resolution index. All URLs are reproduced exactly as provided by the upstream authority without modification, normalization, or protocol inference.

### 官方主站群

<code>bifenguanfang.org.cn</code>

<code>bifenguanfang.cn</code>

<code>bifenguanfang.net.cn</code>

### 备用入口与导航站点

<code>bifenguanwang.com.cn</code>

<code>bifenguanwang.net.cn</code>

<code>bifenguanwang.cn</code>

<code>bifenguanwang.org.cn</code>

These domains are subject to the gateway’s health-check scheduler. The resolver treats each domain as an independent endpoint. In case of duplicate entries in the manifest, the gateway deduplicates based on the normalized domain string while preserving the first occurrence’s metadata.

## 项目结构

The repository follows a modular monorepo layout. Each top-level directory serves a distinct purpose, and internal modules are organized by functional domain. Below is the annotated directory tree.

```
bifeng-gateway/
├── bifeng_gateway/                # Main application package
│   ├── __init__.py               # Package version and exports
│   ├── cli/                      # Command-line interface modules
│   │   ├── __init__.py
│   │   ├── main.py               # Entry point for click group
│   │   ├── serve.py              # Server subcommand implementation
│   │   └── query.py              # Query subcommand with output formatting
│   ├── core/                     # Core business logic
│   │   ├── __init__.py
│   │   ├── resolver.py           # URL resolution and alias expansion logic
│   │   ├── health.py             # Health probe scheduler and result cache
│   │   └── manifest.py           # Manifest loader, validator, and watcher
│   ├── api/                      # RESTful API endpoints
│   │   ├── __init__.py
│   │   ├── routes.py             # Route registration and routing table
│   │   ├── v1/                   # Version 1 API implementation
│   │   │   ├── __init__.py
│   │   │   ├── resources.py      # GET /api/v1/resources endpoint
│   │   │   └── status.py         # GET /api/v1/status endpoint
│   │   └── middleware/           # CORS, rate-limiting, logging middleware
│   ├── static/                   # Static site compiler and assets
│   │   ├── __init__.py
│   │   ├── compiler.py           # Generates HTML from manifest data
│   │   └── templates/            # Jinja2 templates for dashboard pages
│   └── utils/                    # Shared utility functions
│       ├── __init__.py
│       ├── logging.py            # Structured JSON logger configuration
│       └── validators.py         # Pluggable validation routines (SSL, DNS)
├── config/                       # Configuration files
│   ├── manifest.sample.yaml      # Example resource manifest with comments
│   └── logging.yaml              # Log rotation and verbosity settings
├── tests/                        # Test suite
│   ├── unit/                     # Unit tests for individual modules
│   │   ├── test_resolver.py
│   │   └── test_health.py
│   └── integration/              # Integration tests with live HTTP calls
│       └── test_api.py
├── docs/                         # Detailed documentation (see navigation table)
│   ├── user-guide/
│   ├── operator/
│   ├── developer/
│   └── deployment/
├── scripts/                      # Helper scripts for development
│   ├── bootstrap.sh              # Initial environment setup
│   └── run-tests.sh              # Test runner with coverage flags
├── Dockerfile                    # Multi-stage container build definition
├── docker-compose.yml            # Local stack with uvicorn and redis
├── pyproject.toml                # PEP 518 build system configuration
├── setup.cfg                     # Setuptools configuration and metadata
├── README.md                     # This file
└── LICENSE                       # MIT License text
```

## 贡献指南

We welcome contributions from the community. All contributions must adhere to the code of conduct and pass the full CI validation suite. Follow the steps below to submit a change.

1. **Fork the Repository and Set Up Development Environment** – Create a personal fork of the main repository on GitHub. Clone your fork locally and set up the virtual environment as described in the Quick Start section. Ensure all pre-commit hooks are installed by running `pre-commit install` in the root directory.

2. **Identify an Issue or Feature Request** – Browse the issue tracker for open bugs or feature requests labeled "good first issue" or "help wanted". If you plan to implement a new feature that is not yet listed, open a discussion issue first to gather feedback from maintainers.

3. **Create a Descriptive Branch** – Use a branch name that reflects the change, such as `feature/health-check-timeout` or `fix/manifest-parsing-unicode`. Branch from the latest `develop` branch. Do not commit directly to `main` or `develop`.

4. **Implement Changes with Tests and Documentation** – Write code that passes all existing tests. Add new unit or integration tests for any new functionality. Update the relevant documentation files in the `docs/` directory. Ensure that `mypy` passes with zero errors and `black` formatting is applied.

5. **Submit a Pull Request** – Push your branch to your fork and open a pull request against the `develop` branch of the main repository. Fill out the pull request template completely, including a clear description of the change, the motivation, and any potential breaking changes. Request a review from at least two maintainers. Address all review comments before the pull request is merged.

## 常见问题

**Q: How does the gateway handle domain resolution when multiple resources share the same alias?**

A: The manifest schema enforces unique alias names across all entries. If a duplicate alias is detected during manifest loading, the gateway raises a `ManifestValidationError` and refuses to start. This prevents ambiguous resolution. For identical domain strings, the gateway silently deduplicates while preserving the first entry’s metadata and logging a warning to the audit log.

**Q: What happens when a health check fails for a critical resource?**

A: Health check failures are recorded in the internal status cache with a timestamp and failure reason. The resolver continues to return the domain in query results but appends an `"unhealthy"` flag and the last failure message. Operators can configure webhook alerts via the `--alert-webhook` command-line option to receive JSON payloads on status changes. The gateway does not automatically block or remove unhealthy domains; that decision is left to the operator.

**Q: Can I run the gateway entirely offline without external network access?**

A: Yes. The gateway can operate in offline mode by disabling the health-check scheduler with the `--health-check-interval 0` flag. In this mode, all resources are assumed to be reachable, and no external probes are performed. The static site compiler can generate the full dashboard without any network calls. This is particularly useful for air-gapped environments where external connectivity is restricted or undesirable.

## 许可证

This project is licensed under the terms of the MIT License. The license grants permission to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software, subject to the condition that the copyright notice and permission notice are included in all copies or substantial portions of the software. The software is provided "as is", without warranty of any kind, express or implied, including but not limited to the warranties of merchantability, fitness for a particular purpose, and noninfringement. In no event shall the authors or copyright holders be liable for any claim, damages, or other liability, whether in an action of contract, tort, or otherwise, arising from, out of, or in connection with the software or the use or other dealings in the software. For the full license text, refer to the LICENSE file in the repository root.

> 外链数量: 7 | 生成时间: 2026-07-21 18:01:03
