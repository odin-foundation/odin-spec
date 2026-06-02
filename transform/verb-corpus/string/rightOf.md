# %rightOf

text after a delimiter

**Signature:** `%rightOf <string> <delim:string> -> string`

Return the portion of a string after the first occurrence of a delimiter.

**Transform**

```odin
{out}
out = %rightOf @.path "."
```

**In**

```odin
path = "a.b.c"
```

**Out**

```odin
{out}
out = "b.c"
```

**Notes**

- Two required arguments: the string and the delimiter. Only the first delimiter is split on, so the remainder may still contain it.
- If the delimiter is not found, an empty string is returned.
