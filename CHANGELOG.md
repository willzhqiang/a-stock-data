# Changelog

## v4.0.1-local — sync upstream v3.2.3/v3.2.4

### 新增
- 新增东财行业研报 `eastmoney_industry_reports(industry_code="*", max_pages=5, begin="2024-01-01")`。
- 行业研报与个股研报同用 `reportapi.eastmoney.com/report/list`，差异是 `qType=1`；返回 record 可复用 `download_pdf()`。
- `references/endpoint-research.md` 补充行业研报字段、行业码反查方式和 PDF 复用说明。

### 同步上游
- 合并上游 v3.2.3 的行业研报端点能力，端点数 27 → 28。
- 合并上游 v3.2.4 的历史：本地 `tdx_client()` 已在 v4.0.0-local 中实现 mootdx 0.11.x BESTIP 空串崩溃防护，本轮确认并保留渐进式结构。
- 保持本 fork 的“轻量 `SKILL.md` + `references/` + `scripts/`”结构，不回退到上游单文件自包含版本。

### 验证
- `scripts/smoke_test_endpoints.py` 函数清单加入 `eastmoney_industry_reports`。
- 网络 smoke test 加入“东财行业研报”检查项。

## v4.0.0-local — 渐进式披露重构

### 架构调整
- 基于 v3.2.2 稳定线将原本约 2000 行的单文件 `SKILL.md` 改造为渐进式披露 Skill 包：
  - `SKILL.md` 只保留中文路由、数据源优先级、脚本入口和输出契约。
  - `scripts/a_stock_client.py` 承载全部端点实现，并保留 v3.2.2 的公开函数名。
  - `references/` 按端点层、估值公式、工作流和 FAQ 分层存储说明。
- 新增 `scripts/validate_env.py` 环境检查脚本。
- 新增 `scripts/smoke_test_endpoints.py` 迁移完整性 smoke test。
- `scripts/smoke_test_endpoints.py` 扩展为全量网络 best-effort 验证，覆盖行情、研报、信号、资金筹码、新闻、基本面、公告、mootdx 和 iwencai。

### 依赖
- 新增显式依赖 `lxml`，用于 `ths_eps_forecast()` 通过 `pandas.read_html()` 解析同花顺一致预期表格。

### 修复
- 修复 `dragon_tiger_board()` 在无上榜记录时 `buy_data` / `sell_data` 未初始化导致的异常。
- `eastmoney_concept_blocks()` 迁入 v3.2.2 东财 slist 板块归属修复；`baidu_concept_blocks()` 保留为兼容别名。
- `tdx_client()` 迁入本地 mootdx 0.11.7 workaround，规避 `BESTIP.HQ` 空串导致的 `ValueError`。
- `baidu_kline_with_ma()` 增加百度 PAE 空结果保护，接口返回业务错误时给出空结构。
- `cninfo_announcements()` 迁入 v3.2.2 巨潮 orgId 动态映射修复，减少 601xxx 等股票公告查空问题。
- `industry_comparison()` 增加同花顺 HTML fallback，降低东财 push2 瞬态失败影响。
- `em_get()` 增加 3 次串行重试，默认直连东财，失败后再尝试一次环境代理 fallback，提高 push2 / push2his 在不同网络环境下的稳定性。
- `eastmoney_stock_info()` 增加腾讯行情 fallback；当东财 push2 基本面接口不可达时，仍返回名称、价格、市值等基础字段，并附带 `fallback` 和 `error`。
- `eastmoney_fund_flow_minute()` 和 `stock_fund_flow_120d()` 增加 HTTP 备用地址，降低 HTTPS push2 / push2his 瞬态断连影响。

### 兼容性
- 财联社旧 API 仍标记为下线，`cls_telegraph()` 仅作为兼容 stub 保留，默认返回空列表。
- 继续保留东财 `em_get()` 串行限流、防封和会话复用策略。
- 安装方式从“只复制 `SKILL.md`”变为“安装完整 `a-stock-data/` Skill 目录”。

## v3.2.4 — 2026-06-20

### 修复
- 上游新增 `tdx_client()` helper，统一规避 mootdx 0.11.x 全新安装时 `BESTIP.HQ` 空串导致的 `ValueError`。
- 本 fork 在 v4.0.0-local 已实现同等防护，本轮通过合并历史吸收上游版本线。

## v3.2.3 — 2026-06-20

### 新增
- 上游新增东财行业研报端点 `eastmoney_industry_reports()`。
- `industry_code="*"` 拉全行业；传东财行业码如 `1238` 可精确过滤。
- 行业码没有稳定公开码表端点，推荐先全行业拉取后从 `industryName` / `industryCode` 反查。

## v3.2.2 — 2026-06-03

### 修复
- 概念板块归属从失效百度 PAE 接口切换为东财 `slist`。
- 巨潮公告 orgId 改为动态查官方映射表，减少 601xxx 等股票公告查空问题。
- 综合示例改用仍有效的资金流和概念板块函数。

## v3.2.1 — 2026-05-30

### 修复
- 修复东财个股新闻实际返回列表结构导致的解析问题。
- 修复新浪财报三表 `report_list` 结构解析问题。

## v3.2 — 2026-05-30

### 新增
- 建立数据源优先级：mootdx / 腾讯优先，东财只用于独有数据。
- 新增统一东财限流入口 `em_get()`。

### 变更
- 财联社旧 API 下线，全球快讯改用东财全球资讯。
