# %ne

inequality test

**Signature:** `%ne <a:value> <b:value> -> boolean`

Return true when two values are not equal.

**Transform**

```odin
{out}
changed = %ne @.status "active"
same = %ne @.status "closed"
crossType = %ne @.count ##5
```

**In**

```odin
status = "closed"
count = ##5
```

**Out**

```odin
{out}
changed = ?true
same = ?false
crossType = ?false
```

**Notes**

- The exact negation of %eq: same coercion rules apply.
- Use as a condition directly, e.g. %ifElse %ne @.status "active" "flag" "ok".
