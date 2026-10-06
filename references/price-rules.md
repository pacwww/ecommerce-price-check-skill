# Price and exception rules

## Normal cart price

- Verify the exact product ID or link target and specification before recording a price.
- Add quantity 1. The primary cart-line amount is the coupon price under this workflow's business convention.
- Record a gray or struck reference amount as the original price only when visibly attached to the same cart line.
- Do not divide a multi-item total or allocate an unmet order-level threshold across products.

## Conditional prices

- Record ordinary, immediately applicable single-item discounts and describe the visible condition.
- Mark newcomer, first-purchase, member-only, subsidy, trade-in, bundle, minimum-spend and unclear promotional prices `待人工确认` unless the user explicitly defines them as eligible.
- Keep a conditional value in `candidates` when useful, but do not silently promote it to a verified coupon price.

## Enterprise-only fallback

- Prefer the normal cart workflow when available.
- When ordinary add-to-cart is unavailable, the default result is `无法核实` with blank prices.
- If the user asks to capture enterprise-only prices, record the visible detail-page primary value, label its source, and use `价格不完整` unless a true original price is also visible.

## Exceptions

- `无货`: requested SKU is visibly unavailable or sold out. Do not switch SKU.
- `链接失效`: page says removed or invalid.
- `规格不符`: linked product does not match the requested specification. Do not substitute.
- `待人工确认`: product or price eligibility is ambiguous.
- `价格不完整`: usable main price found, but the reference price is absent or the source is a declared fallback.
- `已核查`: exact SKU and required displayed prices are verified.

Keep monetary values numeric. Missing values must be JSON `null`, never `0`.
