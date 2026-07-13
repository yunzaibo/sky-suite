# 三件套版本快照 v0.1.0-integration

**日期**: 2026-07-13  
**里程碑**: 协同验证通过

---

## 版本标记

| 组件 | tag | commit | 状态 |
|------|-----|--------|------|
| sky-browser | v0.1.0-integration | 3f27cb5 | ✅ browser→knife 通用 web 分支 |
| sky-knife | v0.1.0-integration | 993e924 | ✅ 跨件套集成测试 + 验证报告 |
| sky-minds | v0.1.0-integration | 3806601 | ✅ knife→minds 接缝契约 |

---

## 协同验证结果

✅ Test 1: browser→knife (media_handoff → harvest_url_batch)  
✅ Test 2: knife→minds (knife_to_minds_evidence §5 六字段)  
✅ Test 3: 完整链 (browser→knife→minds 端到端)

**详见**: `sky-knife/docs/three-suite-integration-report.md`

---

## 测试覆盖

| 项目 | 测试数 | 状态 |
|------|--------|------|
| sky-browser | 契约测试(check_media_handoff.py) | ✅ 通过 |
| sky-knife | 193 (含 harvest/graph_ingest/集成测试) | ✅ 全绿 |
| sky-minds | (SKILL 级,无单测) | — |

---

## 数据状态(待清理)

- canonical-assets: 227 个(含 173 个哈尔滨测试图)
- llm-wiki: 98 节点 / 281 边
- 待清理: 删除测试数据,保留骨架

---

## 下一步

1. ✅ 版本管理(当前步骤)
2. 🔄 数据清理(删除 data/ 资产,保留结构)
3. 🔄 npm 包骨架(统一安装/更新入口)

---

## 回退方法

```bash
cd sky-browser && git checkout v0.1.0-integration
cd sky-knife && git checkout v0.1.0-integration
cd sky-minds && git checkout v0.1.0-integration
```
