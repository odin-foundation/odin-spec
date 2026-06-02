# %leftOf

text before a delimiter

**Signature:** `%leftOf <string> <delim:string> -> string`

Return the portion of a string before the first occurrence of a delimiter.

**Transform**

```odin
{out}
out = %leftOf @.path "."
```

**In**

```odin
path = "a.b.c"
```

**Out**

```odin
{out}
out = "a"
```

**Notes**

- Two required arguments: the string and the delimiter.
- If the delimiter is not found, the original string is returned unchanged.
