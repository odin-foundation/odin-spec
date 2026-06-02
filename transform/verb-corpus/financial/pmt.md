# %pmt

level payment for a loan

**Signature:** `%pmt <number:principal> <number:rate> <number:periods> -> number`

Compute the constant per-period payment that amortizes a principal over n periods.

**Transform**

```odin
{out}
r = %pmt @.principal @.rate @.periods
zeroRate = %pmt @.principal @.zero @.periods
zeroPeriods = %pmt @.principal @.rate @.noPeriods
```

**In**

```odin
principal = #200000
rate = #0.005
periods = ##360
zero = #0
noPeriods = ##0
```

**Out**

```odin
{out}
r = #1199.1010503055138
zeroRate = #555.5555555555555
zeroPeriods = ~
```

**Notes**

- A 200,000 loan at 0.5% per month over 360 months has a monthly payment near 1199.10.
- The rate is the per-period rate; for an annual rate divide by the number of periods per year.
- A zero rate falls back to principal / periods; periods <= 0 yields ~ (null).

**Avoid**

- `r = %pmt @.principal @.annualRate @.periods ; annualRate = #0.06, periods = ##360`: mixing an annual rate with a monthly period count overstates the payment; convert the rate to per-period (0.06/12 = 0.005)
