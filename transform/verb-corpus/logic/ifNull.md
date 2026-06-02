# %ifNull

fallback when null

**Signature:** `%ifNull <value> <fallback> -> value`

Return a fallback value when the input is null, otherwise the input.

**Transform**

```odin
{out}
name = %ifNull @.nickname "Anonymous"
kept = %ifNull @.realName "Anonymous"
emptyPasses = %ifNull @.blank "Anonymous"
```

**In**

```odin
nickname = ~
realName = "Bob"
blank = ""
```

**Out**

```odin
{out}
name = "Anonymous"
kept = "Bob"
emptyPasses = ""
```

**Notes**

- Only null (~) triggers the fallback; an empty string "" passes through unchanged - use %ifEmpty for that.
- Like %coalesce but with exactly one value and one fallback.
