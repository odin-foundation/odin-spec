# %startOfMonth

first day of the month

**Signature:** `%startOfMonth <date> -> string`

Collapse a date to the first day of its calendar month.

**Transform**

```odin
{out}
d = %startOfMonth @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-06-01"
```

**Notes**

- Returns an ISO date string (day set to 01).
- Month boundaries are computed in UTC.
