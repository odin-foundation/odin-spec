# %drop

skip first N elements

**Signature:** `%drop <array> <integer:count> -> array`

Return the array with the first count elements removed.

**Transform**

```odin
{out}
rest = %drop @.items ##2
dropNone = %drop @.items ##0
negative = %drop @.items ##-1
nonArray = %drop @.notArr ##1
```

**In**

```odin
notArr = "x"

{items[] : ~}
"a"
"b"
"c"
"d"
```

**Out**

```odin
{out}
{.rest[] : ~}
"c"
"d"
{.dropNone[] : ~}
"a"
"b"
"c"
"d"
negative = ~
nonArray = ~
```

**Notes**

- The complement of %take: drops the leading count elements.
