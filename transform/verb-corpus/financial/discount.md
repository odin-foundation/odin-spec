# %discount

present value of a future amount

**Signature:** `%discount <number:futureValue> <number:rate> <number:periods> -> number`

Discount a future value back to today: FV / (1 + r)^n.

**Transform**

```odin
{out}
r = %discount @.fv @.rate @.years
```

**In**

```odin
fv = #16288.946267774423
rate = #0.05
years = ##10
```

**Out**

```odin
{out}
r = ##10000
```

**Notes**

- Discounting is the inverse of %compound; the future value of 10000 at 5% for 10 years discounts back to 10000.
- The rate is a per-period fraction: 5% is 0.05, not 5.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

**Avoid**

- `r = %discount @.fv @.rate ; missing periods`: %discount needs futureValue, rate, and periods; fewer than three arguments yields ~ (null)
