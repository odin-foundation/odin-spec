# %sort

sort a primitive array ascending

**Signature:** `%sort <array> [<string:field>] [<string:direction>] -> array`

Order array values ascending (numeric when all numbers, else lexical).

**Transform**

```odin
{out}
sorted = %sort @.scores
```

**In**

```odin
{scores[] : ~}
##30
##10
##20
```

**Out**

```odin
{out}
{.sorted[] : ~}
##10
##20
##30
```

**Notes**

- Numeric arrays sort numerically; mixed/string arrays sort lexically.
- Optional second/third args sort an object array by field and direction ("asc"/"desc").
