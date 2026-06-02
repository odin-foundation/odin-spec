# %at

element at an index

**Signature:** `%at <array> <integer:index> -> value`

Read a single array element by index, supporting negative indices.

**Transform**

```odin
{out}
last = %at @.items ##-1
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
last = "c"
```

**Notes**

- Negative indices count from the end (##-1 is the last element).
- Out-of-range indices return null (~).
