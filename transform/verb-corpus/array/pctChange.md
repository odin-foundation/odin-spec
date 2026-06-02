# %pctChange

consecutive percentage change

**Signature:** `%pctChange <array> [<integer:periods>] -> array`

Return the fractional change between each element and the prior one.

**Transform**

```odin
{out}
returns = %pctChange @.prices
```

**In**

```odin
{prices[] : ~}
##100
##110
##99
```

**Out**

```odin
{out}
{.returns[] : ~}
~
#0.1
#-0.1
```

**Notes**

- The first element is null; results are fractions (0.1 = +10%).
- A prior value of zero yields null to avoid division by zero.
