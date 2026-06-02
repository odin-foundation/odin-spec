# %fv

future value of an annuity

**Signature:** `%fv <number:payment> <number:rate> <number:periods> -> number`

Accumulate a stream of equal payments at a per-period rate: PMT x ((1+r)^n - 1) / r.

**Transform**

```odin
{out}
r = %fv @.payment @.rate @.periods
```

**In**

```odin
payment = #500
rate = #0.005
periods = ##120
```

**Out**

```odin
{out}
r = #81939.67340322901
```

**Notes**

- Saving 500 per period at 0.5% per period for 120 periods accumulates to about 81939.67.
- First argument is the periodic payment, not a present lump sum (use %compound for a lump sum).
- A zero rate falls back to payment x periods.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.
