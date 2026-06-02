# %npv

net present value of cash flows

**Signature:** `%npv <number:rate> <array:cashflows> -> number`

Discount a series of period cash flows at a rate and sum them, with the first flow at t=0.

**Transform**

```odin
{out}
r = %npv @.rate @.cashflows
```

**In**

```odin
rate = #0.1

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
r = #38.87712587937978
```

**Notes**

- The first cash flow is taken at period 0 (undiscounted); subsequent flows are discounted at (1+rate)^t.
- Argument order is rate first, then the cash-flow array.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

**Avoid**

- `r = %npv @.cashflows @.rate`: passing the array where the scalar rate is expected yields ~ (null); the rate must be the first argument
