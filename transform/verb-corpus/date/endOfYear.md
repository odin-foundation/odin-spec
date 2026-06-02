# %endOfYear

December 31 of the year

**Signature:** `%endOfYear <date> -> string`

Advance a date to the last day of its calendar year.

**Transform**

```odin
{out}
d = %endOfYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-12-31"
```

**Notes**

- Returns an ISO date string (month and day set to 12-31).
- Year boundaries are computed in UTC.
