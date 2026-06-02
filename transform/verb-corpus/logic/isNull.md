# %isNull

null test

**Signature:** `%isNull <value> -> boolean`

Return true when a value is null (~).

**Transform**

```odin
{out}
missing = %isNull @.middleName
```

**In**

```odin
middleName = ~
```

**Out**

```odin
{out}
missing = ?true
```

**Notes**

- Only the null type (~) is null; an empty string "" is not null - use %ifEmpty for that.
- Useful as a guard condition before defaulting with %ifNull.
