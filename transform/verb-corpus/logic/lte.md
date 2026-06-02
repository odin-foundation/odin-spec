# %lte

less than or equal

**Signature:** `%lte <a:value> <b:value> -> boolean`

Return true when the first value is less than or equal to the second.

**Transform**

```odin
{out}
ok = %lte @.score ##100
below = %lte @.low ##100
above = %lte @.high ##100
```

**In**

```odin
score = ##100
low = ##80
high = ##120
```

**Out**

```odin
{out}
ok = ?true
below = ?true
above = ?false
```

**Notes**

- Inclusive at the boundary: 100 <= 100 is true.
- Mirror of %gte.
