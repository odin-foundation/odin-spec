# %drop

skip first N elements

**Signature:** `%drop <array> <integer:count> -> array`

Return the array with the first count elements removed.

**Transform**

```odin
{out}
rest = %drop @.items ##2
```

**In**

```odin
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
```

**Notes**

- The complement of %take: drops the leading count elements.
