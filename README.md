# Sky Suite

**统一知识库与判断系统** — 采集 → 入库 → 检索 → 判断

> **接手方请先读本文件。** 如果你是被指派来跑通本项目的 AI 助手:本文件是你的完整引导。
> 按「环境安装」逐条装好外部工具,再按「快速验证」确认,即可接手。

---

## 这是什么

三个解耦模块,组成一条主线(采集网页/媒体 → 结构化入知识图谱 → 检索取证 → 有据判断):

| 模块 | 角色 | 移植性 |
|------|------|--------|
| **sky-minds** | 判断与推理(思考框架) | 🟢 纯提示词,零依赖,直接可用 |
| **sky-knife** | 知识库构建与检索 | 🟡 需 Python 环境 + graphify |
| **sky-browser** | 浏览器自动化采集 | 🔴 依赖外部浏览器底层,移植性最弱 |

每个模块根目录有 `SKILL.md`(标准 Agent Skills 格式)。**它们本质是给 AI 看的说明书**——任何 AI 读了就能照做;若用 Claude Code,可放进 skills 目录自动触发。

---

## 环境安装

> 核心链路(采集→入库→检索→判断)需要下面「必需」部分。「可选」按需装。

### 必需:基础工具链

```bash
# 1. uv (Python 环境/包管理器,后续都靠它)
curl -LsSf https://astral.sh/uv/install.sh | sh

# 2. Python 3.12
uv python install 3.12

# 3. Node.js (若无) — macOS 用 brew,或 nvm
brew install node        # 或参考 https://nodejs.org
```

### 必需:sky-knife Python 依赖

```bash
cd sky-knife
uv venv .venv --python 3.12
uv pip install --python .venv/bin/python \
  requests readability-lxml lxml beautifulsoup4 Pillow yt-dlp pytest
cd ..
```

### 必需:graphify(知识图谱检索/入图引擎)

脚本内部经 `uv run --with graphifyy graphify ...` **临时运行,无需全局安装**。
如想要全局 `graphify` 命令(可选):

```bash
uv tool install graphifyy
```

### 可选:agent-browser-cli(仅「采集网页」需要)

```bash
npm install -g agent-browser-cli
```

采集时它会驱动**本机真实 Chrome**(复用登录态)。不采集、只用已有知识库做检索/判断时,不需要它。

### 可选:统一命令行 `sky-suite`

```bash
cd cli && npm link && cd ..
# 之后可用 sky-suite init / demo / query / health
```

### ⚠️ sky-browser 的底层(移植性最弱,按需)

`sky-browser` 的完整形态依赖 `bb-browser` / `OpenClaw` 等**为原机器定制的高权限浏览器底层**,这些**不在本包内、也难以直接移植**。

- **核心采集链路不受影响**:`sky-knife` 的网页采集走的是 `agent-browser-cli`(上面「可选」已覆盖),不依赖 bb-browser。
- 只有当你要用 sky-browser 的高级 lane 路由/多站点适配时,才需要那套底层。对方环境若无,**sky-browser 走降级(agent-browser-cli 直连),核心链路照常**。

---

## 快速验证(4 步,开箱即用)

```bash
# 1. 健康检查(确认工具链;会提示缺什么、venv 建没建)
node cli/bin/sky-suite health
#   或 sky-suite health(若已 npm link)

# 2. 初始化知识库
node cli/bin/sky-suite init

# 3. 解压 13 个建筑示例 + 预建图谱(一步到位)
node cli/bin/sky-suite demo

# 4. 检索取证(demo 自带图谱,直接可查)
node cli/bin/sky-suite query "材料 消隐 在地"
#   预期:返回 8 条证据(V&A Dundee / 长沙梅溪湖 / 汤の駅大汤 等),按社区分组
```

> `demo` 的示例包已内含**预建 `graph.json`**,所以 `demo → query` 开箱即通,**不需要**先采集或 ingest。
> 想跑完整「采集→富化→入图」链路,见 `sky-knife/workflows/kb-harvest.md`。

跑单元测试确认代码完整:

```bash
cd sky-knife && .venv/bin/python -m pytest tests/ -q && cd ..
# 预期:全绿(部分需真实浏览器的测试会自动 skip)
```

---

## 主线怎么跑(端到端)

```
sky-browser 采集 ──→ sky-knife 入库+入图 ──→ sky-knife 检索取证 ──→ sky-minds 判断
  (agent-browser-cli)    (graph_ingest)        (knife_to_minds)      (思考框架+引 asset_id)
```

1. **采集**:`sky-knife/scripts/harvest_url_batch.py --url <网址>` (需 agent-browser-cli + Chrome)
2. **富化**:读采集到的正文,写 enrichment JSON(**这步是判断,由 AI/host 做**,见 `sky-knife/workflows/kb-harvest.md`)
3. **入图**:`graph_ingest.py build spec.json`(host 填语义,脚本补 schema)
4. **检索**:`knife_to_minds_evidence.py --question "..."` → 输出 §5 六字段证据
5. **判断**:`sky-minds` 读证据,引 `asset_id` 做有据判断

协同验证细节见 `sky-knife/docs/three-suite-integration-report.md`。

---

## 三档移植性(接手方重点)

| 档 | 内容 | 到了新机器 |
|----|------|-----------|
| 🟢 | 所有 SKILL.md / references / docs / **sky-minds 全部** | 直接能用,纯文本,无本机路径 |
| 🟡 | sky-knife Python 脚本 | 装好上面「必需」即可跑;脚本无写死本机路径 |
| 🔴 | sky-browser 的 bb-browser/OpenClaw 底层 | 本机特有,难移植;核心链路用 agent-browser-cli 替代 |

**最稳的是判断层(sky-minds)**,几乎零依赖;**最需要装环境的是知识库层(sky-knife)**;**最可能水土不服的是采集底层(sky-browser)**。

---

## 数据说明

- 本包**不含真实采集数据**,知识库是空骨架。
- `sky-suite demo` 解压 13 个真实建筑示例(隈研吾 7 + 临水 6)+ 预建图谱,可立即 `query` 体验。
- 采集产物只在本地 `sky-knife/data/`,不随代码分发。

---

## 版本

**v0.1.0-integration** — 三件套协同验证通过(browser→knife→minds 端到端),干净环境部署实测通过(health → init → demo → query)。

License: MIT
