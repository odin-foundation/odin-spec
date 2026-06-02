# %xirr

internal rate of return for dated cash flows

**Signature:** `%xirr <array:amounts> <array:dates> [<number:guess>] -> number`

Find the rate that makes the net present value of dated cash flows zero.

**Transform**

```odin
{out}
r = %xirr @.amounts @.dates
```

**In**

```odin
{amounts[] : ~}
#-1000
#110
#110
#110
#1100

{dates[] : ~}
"2020-01-01"
"2021-01-01"
"2022-01-01"
"2023-01-01"
"2024-01-01"
```

**Out**

```odin
{out}
r = #0.10777982564924497
```

**Notes**

- Solved by Newton-Raphson from a default 10% guess; an optional third argument overrides it.
- Needs at least two cash flows of equal length with the dates; non-convergent series yield ~.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.
