# %startsWith

prefix test

**Signature:** `%startsWith <string> <prefix:string> -> boolean`

Return true when the string begins with the given prefix.

**Transform**

```odin
{out}
out = %startsWith @.name "Hon"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two arguments: the string then the quoted prefix.
- Returns a boolean (? prefix in ODIN output). The match is case-sensitive.
