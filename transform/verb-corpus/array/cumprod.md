# %cumprod

running product

**Signature:** `%cumprod <array> -> array`

Return the cumulative product at each position.

**Transform**

```odin
{out}
growth = %cumprod @.factors
```

**In**

```odin
{factors[] : ~}
#2
#3
#4
```

**Out**

```odin
{out}
{.growth[] : ~}
##2
##6
##24
```

**Notes**

- Each element is the product of all values up to and including that position.
