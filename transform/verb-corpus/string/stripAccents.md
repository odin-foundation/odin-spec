# %stripAccents

remove diacritics

**Signature:** `%stripAccents <string> -> string`

Strip accents and combining marks, leaving base ASCII letters.

**Transform**

```odin
{out}
out = %stripAccents @.name
```

**In**

```odin
name = "café"
```

**Out**

```odin
{out}
out = "cafe"
```

**Notes**

- Single argument. Uses Unicode NFD decomposition, then removes combining diacritical marks.
