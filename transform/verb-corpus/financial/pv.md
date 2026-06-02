# %pv

present value of an annuity

**Signature:** `%pv <number:payment> <number:rate> <number:periods> -> number`

Discount a stream of equal payments to today: PMT x (1 - (1+r)^-n) / r.

**Transform**

```odin
{out}
r = %pv @.payment @.rate @.periods
zeroRate = %pv @.payment @.zero @.periods
```

**In**

```odin
payment = #1000
rate = #0.005
periods = ##120
zero = #0
```

**Out**

```odin
{out}
r = #90073.45332716724
zeroRate = ##120000
```

**Notes**

- Receiving 1000 per period at 0.5% per period for 120 periods is worth about 90073.45 today.
- First argument is the periodic payment; use %discount for a single future amount.
- A zero rate falls back to payment x periods.
