# %match

regex test

**Signature:** `%match <string> <pattern:string> -> boolean`

Return true when a string matches a regular expression.

**Transform**

```odin
{out}
out = %match @.code "^[A-Z]{3}$"
```

**In**

```odin
code = "ABC"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two required arguments: the string and the regex pattern. The pattern is tested (not anchored automatically).
- Returns a boolean (? prefix). Invalid or unsafe patterns return null.
