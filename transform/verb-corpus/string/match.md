# %match

regex test

**Signature:** `%match <string> <pattern:string> -> boolean`

Return true when a string matches a regular expression.

**Transform**

```odin
{out}
hit = %match @.code "^[A-Z]{3}$"
miss = %match @.lower "^[A-Z]{3}$"
bad = %match @.code "(["
```

**In**

```odin
code = "ABC"
lower = "abc"
```

**Out**

```odin
{out}
hit = ?true
miss = ?false
bad = ~
```

**Notes**

- Two required arguments: the string and the regex pattern. The pattern is tested (not anchored automatically).
- Returns a boolean (? prefix). Invalid or unsafe patterns return null.
