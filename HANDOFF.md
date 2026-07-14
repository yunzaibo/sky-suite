# 交接文档 / HANDOFF

> **给接手的 AI 助手**:这份文件是完整交接。读完即可准确把握「这是什么、地址在哪、怎么装、主人要什么」。
> 技术操作细节见 `README.md`;本文件补充**上下文、地址、认证、需求背景**。

---

## 1. 这是什么 · 主人的真实需求

**Sky Suite** —— 一个 **agent-first 的知识库与判断系统**(不是纯向量 RAG)。三件套解耦:

- **sky-browser**:采集(浏览器自动化取网页/媒体证据)
- **sky-knife**:知识库(采集→清洗→富化→**图谱 graphify**→检索取证)
- **sky-minds**:判断(读证据 + 跨域思维框架做**有据判断**)

主线:`采集 → 入库 → 图谱 → 检索 → 判断`。

**主人的核心诉求(务必把握)**:
- 要一个**能跨设备、跨 AI 接手并原样运行**的系统 —— 所以才有本文件。
- 分发方式是**压缩包**(见下),不是让人 clone 一堆 repo。
- 知识库内容是**主人给关键词方向、AI 自主采集真实内容**入库(当前库是空骨架 + 13 个建筑示例,仅供体验)。
- 伦理硬线(GRL-001):采集复用真实 Chrome 登录态,**刻意不做浏览器隐身**;撞验证码/登录墙→停下交人工,**不自动过码、不指纹伪装、不换代理绕**。

---

## 2. 前置条件(装不上多半是这里没满足)

- **系统**:macOS(已验证)/ Linux(命令通用,把 `brew` 换成对应包管理器)/ **Windows 请在 WSL 里操作**(本项目是 bash + POSIX 路径,原生 Windows 不适用)。
- **联网**:首次安装**必须联网** —— Python 依赖、graphify 都是现装现下载(**包内不含它们**,所以整包只有 1.4M)。
- **工具**:`uv`、`python3.12`、`node`(≥18);采集网页另需 `agent-browser-cli`。

---

## 3. 仓库地址(私有)与认证

| 仓库 | 地址 | 说明 |
|------|------|------|
| 主仓库 | `github.com/yunzaibo/sky-suite` 🔒 | monorepo,含三件套 submodule + cli |
| 子模块 | `github.com/yunzaibo/sky-browser` 🔒 | |
| 子模块 | `github.com/yunzaibo/sky-knife` 🔒 | 知识库主体 |
| 子模块 | `github.com/yunzaibo/sky-minds` 🔒 | |

- **全部 PRIVATE**。GitHub 账号:`yunzaibo`。统一版本 tag:`v0.1.0-integration`。
- **从 GitHub 拉取需先认证**:`gh auth login`,否则无权限。**但若你已拿到压缩包,不需要碰 GitHub**(地址仅作溯源/备用)。

---

## 4. 怎么接手(两种方式)

### 方式 A:压缩包(主人的主力方式,推荐)
```bash
tar -xzf sky-suite-v0.1.0-integration.tar.gz
cd sky-suite
# 然后按下面第 5 节安装
```
包内三件套是**实体源码**(非 submodule 空指针),解压即完整,不需联网 clone。

### 方式 B:git clone(需第 3 节认证)
```bash
gh auth login
git clone --recursive https://github.com/yunzaibo/sky-suite.git
```

---

## 5. 安装(详见 README.md)

```bash
# sky-knife Python 依赖(必须建 venv)
cd sky-knife
uv venv .venv --python 3.12
uv pip install --python .venv/bin/python \
  requests readability-lxml lxml beautifulsoup4 Pillow yt-dlp pytest
cd ..

# graphify(检索/图谱引擎)—— 脚本经 `uv run --with graphifyy` 临时运行,无需全局装

# 验证(4 步,开箱即用)
node cli/bin/sky-suite health
node cli/bin/sky-suite init
node cli/bin/sky-suite demo                    # 解压 13 示例 + 预建图谱
node cli/bin/sky-suite query "材料 消隐 在地"   # 应返回约 8 条证据
```

**技能(skills)**:三个模块各有一个 `SKILL.md`(标准 Agent Skills 格式)+ 配套 references/docs。本质是「给 AI 看的说明书」。若你是 Claude Code,把三个 SKILL.md 注册进技能系统(保留各自 references/docs 相对结构);否则确保它们在项目内可被随时读取。**不要改写或精简这些文件**。

**移植性分档**:🟢 sky-minds(纯提示词,零依赖) / 🟡 sky-knife(装好上面即可) / 🔴 sky-browser 的 bb-browser/OpenClaw 底层(原机器特有,难移植;核心采集用 agent-browser-cli 替代)。

---

## 6. 当前状态(截至 v0.1.0-integration)

- ✅ 三件套协同验证通过(browser→knife→minds 端到端);详见 `sky-knife/docs/three-suite-integration-report.md`
- ✅ 干净环境部署实测通过:health→init→demo→query 全通;单测 **211 passed / 6 skipped / 0 failed**
- 知识库为**空骨架**(仅 13 示例);真实内容需按需采集

---

## 7. 验收标准(复刻成功 = 两条都过)

1. `query "材料 消隐 在地"` 返回约 8 条证据
2. `cd sky-knife && .venv/bin/python -m pytest tests/ -q` → **0 failed**(211 passed / 6 skipped 属正常)

遇到 sky-browser 底层缺失、graphify 显示"未全局安装"、instagram 测试 skip —— 都是**正常的**,不影响核心链路。
