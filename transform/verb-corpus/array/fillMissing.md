# %fillMissing

replace null values

**Signature:** `%fillMissing <array> [<value:fill>] [<string:strategy>] -> array`

Substitute a value for null/missing array elements.

**Transform**

```odin
{out}
filled = %fillMissing @.values ##0
```

**In**

```odin
{values[] : ~}
##1
~
##3
```

**Out**

```odin
{out}
{.filled[] : ~}
##1
##0
##3
```

**Notes**

- The default strategy replaces each null with the supplied value.
- A third argument selects "forward", "backward", or "mean" filling instead.
