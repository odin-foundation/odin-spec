# %nper

number of periods

**Signature:** `%nper <number:rate> <number:pmt> <number:pv> <number:fv> -> number`

Solve for how many periods are needed given a rate, payment, present value, and future value.

**Transform**

```odin
{out}
r = %nper @.rate @.pmt @.pv @.fv
```

**In**

```odin
rate = #0.05
pmt = #-1000
pv = #10000
fv = #0
```

**Out**

```odin
{out}
r = #14.206699082890461
```

**Notes**

- Closed-form: log((pmt - rate·fv) / (pmt + rate·pv)) / log(1 + rate).
- Sign convention: the present value (pv) is positive and the payments are negative.
- Argument order is rate, pmt, pv, fv; a zero rate falls back to -(pv + fv) / pmt.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.
