# %nextBusinessDay

next weekday after a date

**Signature:** `%nextBusinessDay <date> -> string`

Return the first Monday-Friday date strictly after the input.

**Transform**

```odin
{out}
friday = %nextBusinessDay @.friday
saturday = %nextBusinessDay @.saturday
midweek = %nextBusinessDay @.wednesday
```

**In**

```odin
friday = 2024-01-19
saturday = 2024-01-20
wednesday = 2024-01-17
```

**Out**

```odin
{out}
friday = "2024-01-22"
saturday = "2024-01-22"
midweek = "2024-01-18"
```

**Notes**

- Always advances at least one day; a Friday rolls forward to Monday.
- Weekends are skipped; there is no holiday calendar.
