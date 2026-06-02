# %rate

interest rate per period

**Signature:** `%rate <number:nper> <number:pmt> <number:pv> <number:fv> -> number`

Solve for the per-period rate given the number of periods, payment, present value, and future value.

**Transform**

```odin
{out}
r = %rate @.nper @.pmt @.pv @.fv
```

**In**

```odin
nper = ##12
pmt = #-1000
pv = #10000
fv = #0
```

**Out**

```odin
{out}
r = #0.02922854076913369
```

**Notes**

- Solved by Newton-Raphson; the result is the per-period rate as a fraction (about 2.92% here).
- Sign convention: an inflow (pv) is positive and the offsetting payments are negative.
- Argument order is nper, pmt, pv, fv.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

**Avoid**

- `r = %rate @.nper @.pmt @.pv ; missing fv`: %rate requires all four arguments; fewer than four yields ~ (null)
