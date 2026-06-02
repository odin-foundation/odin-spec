# %ifNull

fallback when null

**Signature:** `%ifNull <value> <fallback> -> value`

Return a fallback value when the input is null, otherwise the input.

**Transform**

```odin
{out}
name = %ifNull @.nickname "Anonymous"
```

**In**

```odin
nickname = ~
```

**Out**

```odin
{out}
name = "Anonymous"
```

**Notes**

- Only null (~) triggers the fallback; an empty string "" passes through unchanged - use %ifEmpty for that.
- Like %coalesce but with exactly one value and one fallback.
