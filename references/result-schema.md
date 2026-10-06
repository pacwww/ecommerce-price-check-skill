# Task and result schema

`inspect` produces `source`, `generated_at`, `task_count`, and a `tasks` array. A task uses this shape:

```json
{"task_key":"天猫:2","sheet":"天猫","row":2,"platform":"天猫","id":"source id","name":"商品名称","spec":"规格","url":"https://...","existing_coupon_price":null,"existing_original_price":null}
```

Create a results JSON array. Only `task_key` and `status` are required. Use `null` for a missing price.

```json
[
  {
    "task_key": "京东:3",
    "coupon_price": 119,
    "original_price": null,
    "shop": "京东自营",
    "actual_spec": "1008g",
    "conditions": "购物车单项展示价；数量1",
    "queried_at": "2026-09-29T15:30:00+08:00",
    "status": "价格不完整",
    "notes": "未显示灰色参考价",
    "screenshot_path": "evidence/京东-3.png"
  }
]
```

The exporter rejects duplicate or unknown task keys and invalid prices. Omitted price fields preserve source values; explicit `null` clears them.
