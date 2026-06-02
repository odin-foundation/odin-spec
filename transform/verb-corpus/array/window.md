# %window

sliding windows of size n

**Signature:** `%window <array> <integer> -> array`

Return overlapping consecutive slices of length n from an array.

**Transform**

```odin
{out}
panes = %window @.nums ##2
```

**In**

```odin
{nums[] : ~}
##1
##2
##3
##4
```

**Out**

```odin
{out}
{.panes[] : [0], [1]}
##1, ##2
##2, ##3
##3, ##4
```

**Notes**

- Produces (length - n + 1) windows; an n larger than the array yields an empty result.
