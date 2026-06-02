# %lt

less than

**Signature:** `%lt <a:value> <b:value> -> boolean`

Return true when the first value is strictly less than the second.

**Transform**

```odin
{out}
small = %lt @.amount ##1000
```

**In**

```odin
amount = ##250
```

**Out**

```odin
{out}
small = ?true
```

**Notes**

- Mirror of %gt; numbers compare numerically and two strings compare lexically.
- Pair with %gte to build half-open ranges.
