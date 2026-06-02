# %difference

elements only in the first array

**Signature:** `%difference <array> <array> -> array`

Return the distinct elements of the first array that are not in the second.

**Transform**

```odin
{out}
only = %difference @.a @.b
```

**In**

```odin
{a[] : ~}
##1
##2
##3

{b[] : ~}
##2
##3
##4
```

**Out**

```odin
{out.only[] : ~}
##1
```

**Notes**

- Order follows the first array; the result is distinct.
