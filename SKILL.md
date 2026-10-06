---
name: ecommerce-price-check
description: Read a JD.com or Tmall product workbook, verify the linked product and exact specification in a visible logged-in browser, record condition-aware prices and exceptions, and export a traceable XLSX result. Use for Chinese ecommerce price checking from supplied product links; do not use for general product search or private pricing APIs.
---

# Ecommerce Price Check

Turn the user's product workbook into a reviewable price-check result. Treat each worksheet row as an independent task, even when IDs repeat.

## Start the job

1. Locate the workbook and confirm it contains the first six columns `ID、商品名称、规格、跳转链接、券后价、划线价`. Accept XLS or XLSX and up to 100 nonblank linked rows per batch.
2. Run `scripts/workbook_tasks.py inspect <input> --output <tasks.json>` to create a task manifest. Use `工作表:原始行号` as the stable task key.
3. Preserve existing prices by default. Process incomplete rows only, unless the user explicitly asks to recheck prices.
4. Ask the user to complete login or CAPTCHA themselves when needed. Use a visible, logged-in browser session. Do not call private platform pricing APIs, bypass verification, or replace a broken link with a different product.

## Check each row

Work sequentially and at human interaction speed. Open only the row's supplied link and verify title, selected SKU, specification, color, size, weight and package count. Allow exact unit conversions such as `1.008kg = 1008g`; do not treat approximate package weights as exact without visible confirmation.

For products that can be added to an ordinary cart, select the exact SKU, add quantity 1, and read the corresponding cart line. Do not proceed to checkout. Use the large primary amount as `coupon_price` and the visibly displayed small gray or struck amount as `original_price`. Leave an absent price blank rather than writing zero or inferring it.

For enterprise-only products that cannot enter the ordinary cart, record a detail-page price only when the user requests this fallback. Put it in `coupon_price`, leave `original_price` blank unless a genuine gray or struck value is visible, and state `企业专属；详情页展示价；未验证企业成交价` in conditions and notes.

Read [references/price-rules.md](references/price-rules.md) when classifying discounts or exceptions. Read [references/result-schema.md](references/result-schema.md) before creating the results JSON.

## Stop and escalate

Move the row to manual review when the exact SKU cannot be established, the displayed amount has unclear eligibility, or multiple cart lines could match. Record `无货`, `链接失效`, or `规格不符` when the page proves that state. Pause only the affected platform for login expiry, CAPTCHA, or access limits; the other platform may continue. Preserve completed results and resume at the first unfinished task.

## Export and verify

Write one result object per processed task, then run:

```bash
python3 scripts/workbook_tasks.py apply <input> <results.json> --output <result.xlsx>
```

The exporter preserves XLSX workbook layout, original sheets, row order and source columns, fills E/F, and adds audit columns to the right. For XLS input, it creates a new XLSX containing the original cell values because the legacy format cannot be losslessly round-tripped by the helper.

Reopen the output and verify every task remains on its original sheet and row, duplicate IDs remain separate, missing prices are blank, audit fields agree with visible evidence, and the source file was not overwritten.

Report counts by platform and status, describe manual-review rows, and give the exported path. Do not claim unattended automation when browser actions were agent-assisted.
