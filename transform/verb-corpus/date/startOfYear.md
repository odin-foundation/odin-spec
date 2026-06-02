# %startOfYear

January 1 of the year

**Signature:** `%startOfYear <date> -> string`

Collapse a date to the first day of its calendar year.

**Transform**

```odin
{out}
d = %startOfYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-01-01"
```

**Notes**

- Returns an ISO date string (month and day set to 01-01).
- Year boundaries are computed in UTC.
