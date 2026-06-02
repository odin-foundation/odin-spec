# %coalesce

first non-null value

**Signature:** `%coalesce <value> [<value>...] -> value`

Return the first argument that is not null.

**Transform**

```odin
{out}
displayName = %coalesce @.nickname @.firstName "Guest"
allNull = %coalesce @.missing @.alsoMissing "Guest"
firstPresent = %coalesce @.given @.firstName "Guest"
```

**In**

```odin
nickname = ~
firstName = "Alice"
missing = ~
alsoMissing = ~
given = "Carol"
```

**Out**

```odin
{out}
displayName = "Alice"
allNull = "Guest"
firstPresent = "Carol"
```

**Notes**

- Arguments are tried left-to-right; the first non-null wins (here nickname is ~, so firstName is used).
- End with a quoted literal default so the result is never null.
- Only null (~) is skipped; empty strings are kept (use %ifEmpty to treat an empty string as missing).

**Avoid**

- `displayName = %coalesce @.nickname @.firstName`: with no literal fallback the result is ~ when every field is null
