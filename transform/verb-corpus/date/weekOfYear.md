# %weekOfYear

ISO week number

**Signature:** `%weekOfYear <date> -> integer`

Return the ISO 8601 week number (1-53) for a date.

**Transform**

```odin
{out}
mid = %weekOfYear @.date
first = %weekOfYear @.jan
yearEnd = %weekOfYear @.dec
```

**In**

```odin
date = 2024-06-15
jan = 2024-01-04
dec = 2024-12-31
```

**Out**

```odin
{out}
mid = ##24
first = ##1
yearEnd = ##1
```

**Notes**

- Follows ISO 8601 week numbering, anchored on the Thursday of each week.
- Weeks near year boundaries may belong to the adjacent year.
