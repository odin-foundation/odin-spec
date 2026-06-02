# %sort

sort a primitive array ascending

**Signature:** `%sort <array> [<string:field>] [<string:direction>] -> array`

Order array values ascending (numeric when all numbers, else lexical).

**Transform**

```odin
{out}
sorted = %sort @.scores
lexical = %sort @.words
```

**In**

```odin
{scores[] : ~}
##30
##10
##20

{words[] : ~}
"banana"
"apple"
"cherry"
```

**Out**

```odin
{out}
{.sorted[] : ~}
##10
##20
##30
{.lexical[] : ~}
"apple"
"banana"
"cherry"
```

**Notes**

- Numeric arrays sort numerically; mixed/string arrays sort lexically.
- Optional second/third args sort an object array by field and direction ("asc"/"desc").
