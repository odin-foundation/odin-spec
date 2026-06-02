# %unique

remove duplicate values

**Signature:** `%unique <array> -> array`

Drop repeated values from an array (alias of %distinct).

**Transform**

```odin
{out}
uniq = %unique @.tags
allNew = %unique @.distinctTags
nonAdjacent = %unique @.codes
```

**In**

```odin
{tags[] : ~}
"auto"
"home"
"auto"

{distinctTags[] : ~}
"x"
"y"
"z"

{codes[] : ~}
##1
##2
##1
##3
##2
```

**Out**

```odin
{out}
{.uniq[] : ~}
"auto"
"home"
{.allNew[] : ~}
"x"
"y"
"z"
{.nonAdjacent[] : ~}
##1
##2
##3
```

**Notes**

- Alias for %distinct; keeps the first occurrence of each value.
- Field-based dedup is not available through this verb; use %dedupe or %distinct for that.
