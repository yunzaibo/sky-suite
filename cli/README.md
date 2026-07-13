# @sky-suite/cli

**Sky Suite 统一命令行工具** — 一键安装,快速开始

[![npm version](https://img.shields.io/npm/v/@sky-suite/cli)](https://www.npmjs.com/package/@sky-suite/cli)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

---

## 快速安装

```bash
npm install -g @sky-suite/cli
```

**前置依赖**:
- Node.js >= 18
- Python 3.12
- [agent-browser-cli](https://www.npmjs.com/package/agent-browser-cli) (采集时需要)

---

## 使用

### 1. 初始化知识库

```bash
sky-suite init
```

### 2. 采集网页

```bash
sky-suite collect https://www.archdaily.com/...
```

### 3. 体验示例数据集

```bash
sky-suite demo              # 解压 13 个建筑示例
sky-suite query "材料 消隐 在地"  # 检索
```

### 4. 完整工作流

```bash
# 采集 → 富化 → 入图 → 检索
sky-suite collect <url>
# 富化步骤需手动(见下方"完整流程")
sky-suite ingest
sky-suite query "<question>"
```

---

## 命令列表

| 命令 | 说明 |
|------|------|
| `sky-suite init` | 初始化空知识库 |
| `sky-suite collect <url>` | 采集网页(browser→knife) |
| `sky-suite ingest` | 富化+入图 |
| `sky-suite query "<question>"` | 检索(返回§5证据) |
| `sky-suite demo` | 解压示例数据集(13个建筑) |
| `sky-suite health` | 健康检查 |

---

## 完整流程(含富化)

CLI 简化了常用命令,但**富化步骤**(document enrichment)需要判断,暂时需手动执行:

```bash
# 1. 采集
sky-suite collect https://www.archdaily.com/...

# 2. 富化(手动,在 sky-knife 目录)
cd $(npm root -g)/@sky-suite/cli/../../sky-knife
# 按 workflows/kb-harvest.md § 富化步骤执行

# 3. 入图
sky-suite ingest

# 4. 检索
sky-suite query "材料 消隐"
```

**未来版本**会集成富化到 CLI。

---

## 三件套架构

CLI 统一调用三个模块:

- **sky-browser**: 浏览器自动化采集
- **sky-knife**: 知识库构建与检索
- **sky-minds**: 判断与推理

**仓库**: https://github.com/yunzaibo/sky-suite

---

## 开发

### 从源码运行

```bash
git clone --recursive https://github.com/yunzaibo/sky-suite.git
cd sky-suite/cli
npm link
sky-suite health
```

### 本地测试

```bash
cd sky-suite/cli
node bin/sky-suite health
```

---

## 协同验证

✅ **v0.1.0 里程碑**: 三件套协同端到端打通

- browser→knife: media_handoff → harvest
- knife→minds: 检索 → §5 六字段证据
- 完整链: browser→knife→minds

**详见**: [协同验证报告](https://github.com/yunzaibo/sky-suite/blob/main/sky-knife/docs/three-suite-integration-report.md)

---

## License

MIT
