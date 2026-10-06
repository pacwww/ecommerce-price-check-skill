# ecommerce-price-check

一个用于京东、天猫商品表格核价的 Codex Skill。它读取包含商品链接与规格的 XLS/XLSX 表格，通过已登录的可见浏览器核对商品和 SKU，记录购物车价格、优惠条件与异常状态，并导出可追溯的 XLSX 结果。

## 能力

- 将每个“工作表 + 原始行号”作为独立任务，避免重复 ID 或不同规格串行
- 核对商品名称、规格、颜色、尺寸、重量和包装数量
- 区分普通购物车价、划线价、企业专属价及条件优惠
- 标记无货、链接失效、规格不符和待人工确认
- 保留源工作表、行顺序和已有价格，结果导出为新文件
- 缺失价格保持为空，不推算、不写入 0

## 安装

将仓库克隆到 Codex Skills 目录：

```bash
git clone https://github.com/pacwww/ecommerce-price-check-skill.git \
  ~/.codex/skills/ecommerce-price-check
```

安装表格脚本依赖：

```bash
python3 -m pip install -r ~/.codex/skills/ecommerce-price-check/scripts/requirements.txt
```

重启 Codex 后即可使用 `$ecommerce-price-check`。

## 使用

在 Codex 中提供商品表格并输入：

```text
请使用 $ecommerce-price-check 处理这份商品表格，核对京东和天猫价格并导出结果。
```

表格前六列应为：

```text
ID、商品名称、规格、跳转链接、券后价、划线价
```

也可以单独使用确定性脚本生成任务清单与回填结果：

```bash
python3 scripts/workbook_tasks.py inspect input.xlsx --output tasks.json
python3 scripts/workbook_tasks.py apply input.xlsx results.json --output result.xlsx
```

结果 JSON 格式见 [`references/result-schema.md`](references/result-schema.md)，价格判定规则见 [`references/price-rules.md`](references/price-rules.md)。

## 使用边界

该 Skill 使用可见浏览器和用户已登录的会话，不调用平台内部价格接口，不绕过验证码，也不会进入结算或提交订单。验证码、登录失效、规格不明确和条件优惠会进入人工确认。

## License

[MIT](LICENSE)
