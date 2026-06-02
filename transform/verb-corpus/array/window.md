# %window

sliding windows of size n

**Signature:** `%window <array> <integer> -> array`

Return overlapping consecutive slices of length n from an array.

**Transform**

```odin
{out}
pairs = %window @.nums ##2
singles = %window @.nums ##1
```

**In**

```odin
{nums[] : ~}
##1
##2
##3
```

**Out**

```odin
{out}
{.pairs[] : [0], [1]}
##1, ##2
##2, ##3
{.singles[] : [0]}
##1
##2
##3
```

**Notes**

- Produces (length - n + 1) windows; an n larger than the array yields an empty result.
