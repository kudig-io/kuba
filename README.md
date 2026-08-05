# kuba-database · 全网故障数据库

> 收录云原生、AI 原生及互联网公司公开的生产环境重大故障事件，基于 SRE / CRE / FDE 最佳实践进行结构化复盘，构建可检索、可演进的故障知识库。

## 项目定位

- **数据范围**：全网公开的重大故障事件（Outage / Incident / Postmortem），涵盖系统中断、性能劣化、数据丢失、安全事件引发的可用性问题等；时间跨度从 2007 年 AWS EC2 首次重大故障到 2026 年 AWS 物理设施热失控事故，涵盖全球主要云服务商（AWS、Azure、GCP、阿里云、Cloudflare 等）与互联网平台（Meta、腾讯、Akamai 等）的重大故障。
- **数据来源**：官方复盘报告（Postmortem / RCA）、状态页归档、工程博客、监管报告等**可验证的公开信息**。
- **分析框架**：SRE 无指责复盘（Blameless Postmortem）+ CRE 客户可靠性视角 + FDE 现场数据工程方法。
- **存储形式**：纯 Markdown + YAML Frontmatter 元数据，Git 版本控制，无数据库依赖，可被任何工具索引。

## 目录结构

```
kuba/
├── README.md                  # 本文档
├── CONTRIBUTING.md            # 贡献指南、命名规范、版本控制与更新机制
├── INDEX.md                   # 总索引（由 tools/build-index.py 自动生成）
├── docs/
│   ├── methodology/           # 复盘方法论
│   │   ├── README.md          # 方法论总览与三者关系
│   │   ├── sre-postmortem.md  # SRE 无指责复盘方法
│   │   ├── cre-practices.md   # CRE 客户可靠性工程实践
│   │   └── fde-analysis.md    # FDE 现场数据工程分析方法
│   └── severity-and-taxonomy.md  # 故障分级标准与分类体系（领域/根因分类）
├── templates/
│   ├── incident-report.md     # 标准故障复盘报告模板（完整版）
│   └── quick-entry.md         # 快速记录模板（信息不全时先占位）
├── incidents/                 # 故障案例库（按技术领域分文件夹，当前 74 个案例）
│   ├── README.md              # 领域分类说明
│   ├── cloud-infrastructure/  # 云基础设施（AWS / Azure / GCP / 阿里云 …）
│   ├── cdn-edge/              # CDN 与边缘网络
│   ├── networking-dns/        # 网络 / DNS / BGP
│   ├── database-storage/      # 数据库与存储
│   ├── container-orchestration/  # 容器编排与服务发现
│   ├── messaging-streaming/   # 消息与流处理
│   ├── identity-access/       # 身份认证与访问控制
│   ├── saas-platforms/        # SaaS 平台
│   ├── observability/         # 可观测性平台
│   ├── ai-ml-services/        # AI / ML 服务
│   └── security-services/     # 安全产品引发的可用性事件
└── tools/
    ├── search.sh              # 多维度检索（关键词/公司/领域/根因/等级）
    ├── new-incident.sh        # 从模板创建新案例（自动填充元数据骨架）
    └── build-index.py         # 扫描全库，重建 INDEX.md 与统计信息
```

## 快速开始

### 检索故障案例

```bash
# 全文关键词检索
./tools/search.sh "BGP"

# 按公司检索
./tools/search.sh --company cloudflare

# 按根因分类检索（分类见 docs/severity-and-taxonomy.md）
./tools/search.sh --cause change-management

# 按严重等级 + 关键词组合检索
./tools/search.sh --severity SEV-1 "DNS"

# 按技术领域检索
./tools/search.sh --domain database-storage
```

### 新增故障案例

```bash
# 用法: ./tools/new-incident.sh <领域目录> <YYYY-MM-DD> <公司> <标题slug>
./tools/new-incident.sh cloud-infrastructure 2026-07-01 example-cloud api-outage
# 生成: incidents/cloud-infrastructure/2026-07-01-example-cloud-api-outage.md
```

### 重建索引

```bash
python3 tools/build-index.py    # 重新生成 INDEX.md（按年份/领域/根因/等级多维索引 + 统计）
```

## 案例数据规范（摘要）

每个案例文件 = **YAML Frontmatter（结构化元数据，供机器检索）** + **Markdown 正文（复盘内容，供人阅读）**。

正文必备章节：`摘要` → `影响评估` → `时间线` → `技术细节与根因分析` → `解决过程` → `经验教训` → `预防与改进措施` → `SRE/CRE/FDE 视角速览` → `参考资料`。

完整字段定义见 [templates/incident-report.md](templates/incident-report.md)，分级与分类枚举见 [docs/severity-and-taxonomy.md](docs/severity-and-taxonomy.md)。

## 数据质量原则

1. **只收录可验证的公开信息**，每个案例必须附带至少一个权威来源链接（官方复盘优先）。
2. **无指责（Blameless）**：聚焦系统性因素，不针对个人；转述官方报告时保持中立。
3. **区分事实与推断**：官方未披露的细节若来自第三方分析，须明确标注"第三方推断"。
4. **持续更新**：官方后续披露新细节时更新案例并递增 `last_updated`，历史通过 Git 追溯。

## License 与免责声明

本库内容为对公开信息的整理与再分析，仅用于工程学习与可靠性研究。各案例版权与事实解释权归原披露方所有；如有事实性错误请提 Issue 修正。
