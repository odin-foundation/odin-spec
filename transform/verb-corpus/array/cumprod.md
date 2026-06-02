# %cumprod

running product

**Signature:** `%cumprod <array> -> array`

Return the cumulative product at each position.

**Transform**

```odin
{out}
growth = %cumprod @.factors
withZero = %cumprod @.withZero
nonArray = %cumprod @.notArr
```

**In**

```odin
notArr = "x"

{factors[] : ~}
#2
#3
#4

{withZero[] : ~}
#2
#0
#5
```

**Out**

```odin
{out}
{.growth[] : ~}
##2
##6
##24
{.withZero[] : ~}
##2
##0
##0
nonArray = ~
```

**Notes**

- Each element is the product of all values up to and including that position.
