# %irr

internal rate of return

**Signature:** `%irr <array:cashflows> <number:guess?> -> number`

Find the discount rate that makes the net present value of a cash-flow series zero.

**Transform**

```odin
{out}
r = %irr @.cashflows
```

**In**

```odin
{cashflows[] : ~}
#-100
#30
#40
#50
#60
```

**Out**

```odin
{out}
r = #0.24888335658556993
```

**Notes**

- Solved by Newton-Raphson from a default 10% guess; the result is the per-period rate as a fraction.
- Needs at least two cash flows; an optional second argument overrides the initial guess.
- Series with no sign change, or that fail to converge, yield ~ (null).
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.
