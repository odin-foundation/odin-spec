# %fillMissing

replace null values

**Signature:** `%fillMissing <array> [<value:fill>] [<string:strategy>] -> array`

Substitute a value for null/missing array elements.

**Transform**

```odin
{out}
filled = %fillMissing @.values ##0
forward = %fillMissing @.gaps ##0 "forward"
noNulls = %fillMissing @.full ##0
```

**In**

```odin
{values[] : ~}
##1
~
##3

{gaps[] : ~}
##1
~
~
##4

{full[] : ~}
##5
##6
```

**Out**

```odin
{out}
{.filled[] : ~}
##1
##0
##3
{.forward[] : ~}
##1
##1
##1
##4
{.noNulls[] : ~}
##5
##6
```

**Notes**

- The default strategy replaces each null with the supplied value.
- A third argument selects "forward", "backward", or "mean" filling instead.
