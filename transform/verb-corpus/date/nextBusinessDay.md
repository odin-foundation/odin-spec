# %nextBusinessDay

next weekday after a date

**Signature:** `%nextBusinessDay <date> -> string`

Return the first Monday-Friday date strictly after the input.

**Transform**

```odin
{out}
d = %nextBusinessDay @.start
```

**In**

```odin
start = 2024-01-19
```

**Out**

```odin
{out}
d = "2024-01-22"
```

**Notes**

- Always advances at least one day; a Friday rolls forward to Monday.
- Weekends are skipped; there is no holiday calendar.
