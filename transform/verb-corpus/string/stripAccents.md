# %stripAccents

remove diacritics

**Signature:** `%stripAccents <string> -> string`

Strip accents and combining marks, leaving base ASCII letters.

**Transform**

```odin
{out}
out = %stripAccents @.name
many = %stripAccents @.full
plain = %stripAccents @.ascii
```

**In**

```odin
name = "café"
full = "José García"
ascii = "Honda"
```

**Out**

```odin
{out}
out = "cafe"
many = "Jose Garcia"
plain = "Honda"
```

**Notes**

- Single argument. Uses Unicode NFD decomposition, then removes combining diacritical marks.
