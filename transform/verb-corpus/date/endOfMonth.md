# %endOfMonth

last day of the month

**Signature:** `%endOfMonth <date> -> string`

Advance a date to the final day of its calendar month.

**Transform**

```odin
{out}
d = %endOfMonth @.date
```

**In**

```odin
date = 2024-02-15
```

**Out**

```odin
{out}
d = "2024-02-29"
```

**Notes**

- Leap years are honored - February 2024 ends on the 29th.
- Returns an ISO date string.
