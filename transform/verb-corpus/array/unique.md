# %unique

remove duplicate values

**Signature:** `%unique <array> [<string:field>] -> array`

Drop repeated values from an array (alias of %distinct).

**Transform**

```odin
{out}
uniq = %unique @.tags
```

**In**

```odin
{tags[] : ~}
"auto"
"home"
"auto"
```

**Out**

```odin
{out}
{.uniq[] : ~}
"auto"
"home"
```

**Notes**

- Alias for %distinct; keeps the first occurrence of each value.
