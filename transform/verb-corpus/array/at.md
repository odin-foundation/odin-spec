# %at

element at an index

**Signature:** `%at <array> <integer:index> -> value`

Read a single array element by index, supporting negative indices.

**Transform**

```odin
{out}
last = %at @.items ##-1
first = %at @.items ##0
outOfRange = %at @.items ##9
beyondNeg = %at @.items ##-9
nonArray = %at @.notArr ##0
```

**In**

```odin
notArr = "x"

{items[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
last = "c"
first = "a"
outOfRange = ~
beyondNeg = ~
nonArray = ~
```

**Notes**

- Negative indices count from the end (##-1 is the last element).
- Out-of-range indices return null (~).
