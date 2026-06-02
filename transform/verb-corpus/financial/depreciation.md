# %depreciation

straight-line depreciation per period

**Signature:** `%depreciation <number:cost> <number:salvage> <number:life> -> number`

Compute the constant per-period depreciation: (cost - salvage) / life.

**Transform**

```odin
{out}
r = %depreciation @.cost @.salvage @.life
salvageOverCost = %depreciation @.lowCost @.highSalvage @.life
zeroLife = %depreciation @.cost @.salvage @.zero
```

**In**

```odin
cost = #10000
salvage = #2000
life = ##5
lowCost = #2000
highSalvage = #10000
zero = ##0
```

**Out**

```odin
{out}
r = ##1600
salvageOverCost = ~
zeroLife = ~
```

**Notes**

- An asset costing 10000 with 2000 salvage over a 5-year life depreciates 1600 per year.
- A life <= 0, or a salvage greater than cost, yields ~ (null).

**Avoid**

- `r = %depreciation @.cost @.salvage @.life ; salvage = #10000, cost = #2000`: when salvage exceeds cost the result is ~ (null), not a negative depreciation
