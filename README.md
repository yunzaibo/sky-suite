# Sky Suite

**统一知识库与判断系统** — 采集 → 入库 → 检索 → 判断

[![Version](https://img.shields.io/badge/version-0.1.0--integration-blue)](https://github.com/yunzaibo/sky-suite/releases/tag/v0.1.0-integration)

---

## 三件套架构

| 模块 | 仓库 | 功能 |
|------|------|------|
| **sky-browser** | [yunzaibo/sky-browser](https://github.com/yunzaibo/sky-browser) | 浏览器自动化采集 |
| **sky-knife** | [yunzaibo/sky-knife](https://github.com/yunzaibo/sky-knife) | 知识库构建与检索 |
| **sky-minds** | [yunzaibo/sky-minds](https://github.com/yunzaibo/sky-minds) | 判断与推理 |

---

## 快速开始

### 安装

```bash
# 克隆主仓库(含 submodules)
git clone --recursive https://github.com/yunzaibo/sky-suite.git
cd sky-suite

# 安装依赖(各模块独立安装)
cd sky-browser && npm install && cd ..
cd sky-knife && python3.12 -m venv .venv && .venv/bin/pip install -r requirements.txt && cd ..
```

### 使用流程

```bash
# 1. browser 采集
cd sky-browser
python3 -c "from src.media_handoff import execute_media_handoff; ..."

# 2. knife 入图
cd ../sky-knife
python3.12 scripts/graph_ingest.py build spec.json

# 3. knife 检索
python3.12 scripts/knife_to_minds_evidence.py --question "..."

# 4. minds 判断
cd ../sky-minds
# (在 SKILL 中引用 knife 返回的证据)
```

---

## 协同验证

✅ **v0.1.0-integration 里程碑**: 三件套协同端到端打通

- Test 1: browser→knife (media_handoff → harvest)
- Test 2: knife→minds (检索 → §5 六字段证据)
- Test 3: 完整链 (browser→knife→minds)

**详见**: [sky-knife/docs/three-suite-integration-report.md](sky-knife/docs/three-suite-integration-report.md)

---

## 版本管理

**统一版本**: 三件套配套发版,避免组合兼容性问题

- **当前版本**: v0.1.0-integration
- **下一步**: npm 包分发(`@sky-suite/cli`)

---

## 开发

### 更新 submodules

```bash
git submodule update --remote --merge
```

### 各模块独立开发

```bash
cd sky-knife
git checkout -b feature/xxx
# ... 开发
git push origin feature/xxx
```

### 发布新版本

```bash
# 各子模块打 tag
cd sky-browser && git tag v0.2.0 && git push --tags && cd ..
cd sky-knife && git tag v0.2.0 && git push --tags && cd ..
cd sky-minds && git tag v0.2.0 && git push --tags && cd ..

# 主仓库更新 submodule 引用并打 tag
git add sky-browser sky-knife sky-minds
git commit -m "chore: bump to v0.2.0"
git tag v0.2.0
git push --tags
```

---

## License

MIT
