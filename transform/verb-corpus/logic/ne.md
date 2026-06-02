# %ne

inequality test

**Signature:** `%ne <a:value> <b:value> -> boolean`

Return true when two values are not equal.

**Transform**

```odin
{out}
changed = %ne @.status "active"
```

**In**

```odin
status = "closed"
```

**Out**

```odin
{out}
changed = ?true
```

**Notes**

- The exact negation of %eq: same coercion rules apply.
- Use as a condition directly, e.g. %ifElse %ne @.status "active" "flag" "ok".
