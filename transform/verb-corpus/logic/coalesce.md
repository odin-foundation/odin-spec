# %coalesce

first non-null value

**Signature:** `%coalesce <value> [<value>...] -> value`

Return the first argument that is not null or empty.

**Transform**

```odin
{out}
displayName = %coalesce @.nickname @.firstName "Guest"
```

**In**

```odin
nickname = ~
firstName = "Alice"
```

**Out**

```odin
{out}
displayName = "Alice"
```

**Notes**

- Arguments are tried left-to-right; the first non-null wins (here nickname is ~, so firstName is used).
- End with a quoted literal default so the result is never null.

**Avoid**

- `displayName = %coalesce @.nickname @.firstName`: with no literal fallback the result is ~ when every field is null
