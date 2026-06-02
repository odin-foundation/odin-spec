# %gte

greater than or equal

**Signature:** `%gte <a:value> <b:value> -> boolean`

Return true when the first value is greater than or equal to the second.

**Transform**

```odin
{out}
ok = %gte @.score ##60
below = %gte @.low ##60
above = %gte @.high ##60
```

**In**

```odin
score = ##60
low = ##59
high = ##90
```

**Out**

```odin
{out}
ok = ?true
below = ?false
above = ?true
```

**Notes**

- Inclusive at the boundary: 60 >= 60 is true.
- Combine with %lt for inclusive-lower, exclusive-upper bands.
