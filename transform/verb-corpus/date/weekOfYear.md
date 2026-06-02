# %weekOfYear

ISO week number

**Signature:** `%weekOfYear <date> -> integer`

Return the ISO 8601 week number (1-53) for a date.

**Transform**

```odin
{out}
n = %weekOfYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
n = ##24
```

**Notes**

- Follows ISO 8601 week numbering, anchored on the Thursday of each week.
- Weeks near year boundaries may belong to the adjacent year.
