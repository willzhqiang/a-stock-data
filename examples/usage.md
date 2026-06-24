# 调用示例

本文件只放可复制的命令行和 Python 调用示例。工作流程说明放在 `references/workflows-*.md`，端点字段说明放在 `references/endpoint-*.md`。

## 安装依赖

```bash
uv run --with requests --with pandas --with lxml --with stockstats --with mootdx python scripts/validate_env.py
```

项目环境中已安装依赖时，也可直接运行：

```bash
python scripts/validate_env.py
```

## 环境变量

```bash
export IWENCAI_API_KEY="your_key_here"
export IWENCAI_BASE_URL="https://openapi.iwencai.com"
```

`IWENCAI_API_KEY` 只用于 iwencai 语义研报搜索。没有 key 时，其他端点仍可使用。

## 命令行调用

```bash
python scripts/a_stock_client.py tencent_quote 600519
python scripts/a_stock_client.py baidu_kline_with_ma 600519
python scripts/a_stock_client.py eastmoney_reports 600519 --kwargs '{"max_pages": 1}'
python scripts/a_stock_client.py eastmoney_industry_reports '*' --kwargs '{"max_pages": 1}'
python scripts/a_stock_client.py eastmoney_concept_blocks 600519
python scripts/a_stock_client.py full_valuation 688017
```

## Python 调用

```python
from scripts.a_stock_client import (
    eastmoney_industry_reports,
    full_valuation,
    tencent_quote,
)

quote = tencent_quote(["600519"])
industry_reports = eastmoney_industry_reports("*", max_pages=1)
valuation = full_valuation("688017")
```

## 验证

无网络基础校验：

```bash
python scripts/smoke_test_endpoints.py
```

真实网络 best-effort 校验：

```bash
python scripts/smoke_test_endpoints.py --network
```
