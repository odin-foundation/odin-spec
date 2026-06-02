# %endsWith

suffix test

**Signature:** `%endsWith <string> <suffix:string> -> boolean`

Return true when the string ends with the given suffix.

**Transform**

```odin
{out}
out = %endsWith @.name "da"
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

- Two arguments: the string then the quoted suffix.
- Returns a boolean (? prefix in ODIN output). The match is case-sensitive.
