# %xnpv

net present value of dated cash flows

**Signature:** `%xnpv <number:rate> <array:amounts> <array:dates> -> number`

Discount cash flows that occur on specific dates and sum them, on a 365-day basis.

**Transform**

```odin
{out}
v = %xnpv @.rate @.amounts @.dates
```

**In**

```odin
rate = #0.09

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
v = #57.460446077146344
```

**Notes**

- Each amount is discounted by (1+rate)^((date - firstDate)/365); the first date is the t=0 reference.
- amounts and dates must have equal length; otherwise the result is ~.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.
