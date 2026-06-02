# %compound

future value of a lump sum

**Signature:** `%compound <number:principal> <number:rate> <number:periods> -> number`

Grow a principal at a per-period rate over n periods: P x (1 + r)^n.

**Transform**

```odin
{out}
r = %compound @.principal @.rate @.years
```

**In**

```odin
principal = #10000
rate = #0.05
years = ##10
```

**Out**

```odin
{out}
r = #16288.946267774423
```

**Notes**

- Arguments are principal, then rate, then periods, in that order.
- The rate is a per-period fraction: 5% is 0.05, not 5.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

**Avoid**

- `r = %compound @.principal @.rate @.years ; rate = #5`: a rate given as 5 (meaning 500%) instead of 0.05 returns ~604661760000 - express the rate as a fraction
- `r = %compound @.principal @.years @.rate`: rate and periods are swapped; the engine computes 10000 x (1+10)^0.05 = ~11273.78, not the intended growth
