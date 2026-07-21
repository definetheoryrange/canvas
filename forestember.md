# Bifeng Resource Navigator

Bifeng Resource Navigator is a curated technical documentation and external link aggregation platform designed for developers, system administrators, and security researchers who need reliable, structured access to domain registration records, network infrastructure documentation, and institutional resource directories. The project addresses the common challenge of maintaining an organized, version-controlled, and rapidly searchable collection of external technical references that are frequently updated or relocated across different authoritative sources.

Target users include DevOps engineers managing infrastructure documentation, cybersecurity analysts tracking domain ownership histories, and technical writers maintaining cross-referenced knowledge bases. The platform provides a unified interface to access distributed resources while preserving original source integrity and offering contextual navigation cues.

## 功能概览

- **Resource Indexing Engine**: Automated extraction and categorization of external URLs into semantic groups such as institutional portals, network administration interfaces, and documentation mirrors.

- **Link Validation Pipeline**: Periodic health checks on all registered URLs to detect broken links, certificate expiry, or domain resolution changes, with status reporting.

- **Structured Metadata Annotation**: Each resource entry supports custom tags, optional descriptions, and relationship mapping to other entries within the catalog.

- **Full-Text Search Over Metadata**: Search across titles, tags, and custom annotations without crawling external content, ensuring fast offline-available lookups.

- **ASCII Directory Tree Generator**: Built-in utility to produce human-readable project structure maps for documentation repositories, aiding new contributors in navigation.

- **Versioned Snapshot Export**: Ability to export the entire resource catalog as a static Markdown or JSON file for backup, offline use, or integration into CI/CD workflows.

- **Multi-Format Output Adapter**: Supports rendering the resource list as plain Markdown, HTML table, or structured YAML for downstream tooling.

## 应用场景

- **Infrastructure Documentation Maintenance**: A DevOps team maintains a central repository of links to internal network administration panels, DNS registrar portals, and SSL certificate management interfaces. Bifeng Resource Navigator provides a single source of truth for these frequently rotated URLs, reducing the time spent hunting for correct addresses during incident response.

- **Security Research Reference Aggregation**: A security analyst tracks multiple domain registration authorities and institutional pages to monitor changes in WHOIS records or public notices. The navigator's structured catalog allows rapid cross-referencing between different domain variants (e.g., .org.cn, .com.cn, .net.cn) without manual copy-paste errors.

- **Technical Writing and Documentation Cross-Linking**: A technical writer compiling a guide on Chinese domain registration practices uses the navigator to organize official registry portals, policy documents, and example records. The link validation pipeline ensures all references remain current before publication.

- **Open Source Project Dependency Mapping**: An open source maintainer uses the navigator to record external service endpoints that their project depends on, providing clear documentation for users who need to configure firewall rules or proxy settings.

## 快速开始

Clone the repository, install dependencies, and run the development server locally.

```bash
git clone https://github.com/bifeng-project/bifeng-navigator.git
cd bifeng-navigator
pip install -r requirements.txt
python app.py --init-db
python app.py --serve --port 8080
```

For production deployment, refer to the deployment guide in the `docs/deployment/` directory.

## 安装要求

The following dependencies are required to run the Bifeng Resource Navigator locally or in a production environment.

| 依赖组件 | 必需版本 | 说明 |
|---------|---------|------|
| Python | >=3.9, <3.12 | Core runtime; 3.11 recommended for performance |
| SQLite | >=3.35 | Embedded database for metadata storage; no external server required |
| requests | >=2.31.0 | HTTP client for link validation and external fetching |
| PyYAML | >=6.0 | YAML parser for configuration and export formats |
| markdown | >=3.5 | Renderer for generating HTML previews from Markdown catalogs |
| pytest | >=7.4 | Testing framework; required only for development and CI |
| black | >=23.0 | Code formatter; optional but recommended for contribution |
| mypy | >=1.0 | Static type checker; used in pre-commit hooks |

## 文档导航

The project documentation is organized into four major layers, each addressing a distinct audience concern.

| 层面 | 目录 | 回答的问题 |
|------|------|-----------|
| 用户入门 | `docs/user/quickstart.md` | How do I install the navigator and add my first resource? |
| 维护操作 | `docs/admin/link-validation.md` | How do I schedule link health checks and interpret failure logs? |
| 开发扩展 | `docs/dev/catalog-schema.md` | What metadata fields are available, and how can I add custom tags? |
| 架构参考 | `docs/architecture/data-flow.md` | How does the indexing pipeline process external URLs and store metadata? |

Additional documentation is available in `docs/contrib/` for special topics such as integrating with static site generators or exporting catalogs to Confluence.

## 资源列表

This section enumerates all external resources officially registered in the Bifeng Resource Navigator catalog. Each entry is preserved exactly as provided by the original source, without any modification to protocol, domain formatting, or path components.

### 官方机构门户

- <code>bifenguanfang.org.cn</code>
- <code>bifenguanfang.cn</code>
- <code>bifenguanfang.net.cn</code>

### 网络管理控制台

- <code>bifenguanwang.com.cn</code>
- <code>bifenguanwang.net.cn</code>
- <code>bifenguanwang.cn</code>
- <code>bifenguanwang.org.cn</code>

These URLs are maintained as separate entries to reflect distinct administrative interfaces or mirror sites. Users should consult the link validation status panel for each entry to determine current availability.

## 项目结构

The project follows a modular monorepo layout with clear separation between core logic, user interface, documentation, and test assets. Each directory includes an `__init__.py` or `.keep` file as appropriate.

```
bifeng-navigator/
├── app.py                      # Main application entry point; CLI and web server boot
├── config/
│   ├── default.yaml            # Base configuration (port, DB path, validation interval)
│   └── schema.json             # JSON Schema for catalog metadata validation
├── core/
│   ├── indexer.py              # Resource indexing engine; parses and normalizes URLs
│   ├── validator.py            # Link validation worker; async HTTP checks with retry
│   └── exporter.py             # Multi-format exporter (Markdown, JSON, YAML)
├── models/
│   ├── resource.py             # ORM for Resource entity (URL, tags, status, last_seen)
│   └── category.py             # Category tree for organizing resources hierarchically
├── web/
│   ├── routes/                 # Flask/Starlette route handlers for UI and API
│   │   ├── catalog.py          # List, add, edit, delete resource endpoints
│   │   └── status.py           # Validation status dashboard endpoint
│   ├── templates/              # Jinja2 templates for web interface
│   │   ├── index.html          # Main catalog listing with search bar
│   │   └── detail.html         # Individual resource detail view
│   └── static/                 # CSS and client-side JavaScript for responsive layout
├── scripts/
│   ├── init_db.py              # SQL schema creation and seed data loader
│   └── validate_all.py         # One-off validation runner for CI pipelines
├── tests/
│   ├── unit/                   # Unit tests for core modules and models
│   └── integration/            # End-to-end tests with temporary SQLite database
├── docs/                       # All user and developer documentation
│   ├── user/                   # Quickstart, CLI reference, FAQ
│   ├── admin/                  # Deployment, monitoring, backup strategies
│   ├── dev/                    # Code style, commit guidelines, architecture decisions
│   └── architecture/           # System context diagrams and data flow descriptions
└── README.md                   # This file; project overview and entry point
```

## 贡献指南

We welcome contributions from the community to improve the navigator's core functionality, expand test coverage, or refine documentation.

1. **Fork the Repository and Create a Feature Branch**: Fork the main repository to your personal account, then create a branch with a descriptive name such as `feature/add-http2-validation` or `fix/unicode-url-parsing`.

2. **Run the Development Environment**: Set up a local virtual environment using `python -m venv venv`, activate it, and install development dependencies via `pip install -e .[dev]`. Run the test suite with `pytest` to ensure all existing tests pass.

3. **Implement Changes with Type Annotations and Tests**: All new functions must include Python type hints. Add unit tests for any new logic in the `tests/unit/` directory. For bug fixes, include a regression test that reproduces the issue.

4. **Update Documentation Correspondingly**: If your change affects user-facing behavior or configuration, update the relevant `docs/` files. For new resource metadata fields, update `docs/dev/catalog-schema.md`.

5. **Submit a Pull Request with a Clear Change Log**: Push your branch and open a pull request against the `main` branch. Fill out the provided PR template, including a bullet-point change log and the output of `pytest --cov=core/`. The maintainers will review within five business days.

## 常见问题

**Q: How often does the link validation pipeline run, and can I manually trigger it?**

A: By default, the validation pipeline runs every 24 hours via a background scheduler when the web server is active. You can manually trigger an ad-hoc validation for all resources by running `python app.py --validate-now`. Individual resource validation is also available through the web dashboard's "Check Now" button on the detail view.

**Q: What happens if a URL fails validation, and how is that reflected in the catalog?**

A: Failed URLs are marked with a `last_status` field set to `unreachable` and a `last_seen` timestamp of the last successful check. The catalog listing displays a warning icon next to such entries. After three consecutive failures, the entry is moved to a "Suspended" filter category and excluded from default views, though it remains in the database and search results with a prominent notice.

**Q: Can I add custom metadata fields beyond the default tags and description?**

A: Yes. The catalog schema is defined in `config/schema.json` and supports an `extra_metadata` JSON blob per resource. You can add any key-value pair via the API or the web form's "Advanced" section. The search index automatically includes these extended fields. To avoid breaking exports, ensure your custom fields do not conflict with reserved keys: `url`, `title`, `tags`, `category`, `status`, `last_seen`.

## 许可证

This project is licensed under the MIT License. See the `LICENSE` file in the repository root for the full text. The MIT License permits unrestricted use, distribution, and modification with proper attribution, and is compatible with both open source and proprietary downstream projects.

> 外链数量: 7 | 生成时间: 2026-07-21 18:01:03
