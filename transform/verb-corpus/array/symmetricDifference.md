# %symmetricDifference

elements in exactly one array

**Signature:** `%symmetricDifference <array> <array> -> array`

Return the distinct elements that appear in exactly one of the two arrays.

**Transform**

```odin
{out}
xor = %symmetricDifference @.a @.b
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
{out}
{.xor[] : ~}
##1
##4
```

**Notes**

- First-array exclusives precede second-array exclusives; the result is distinct.
