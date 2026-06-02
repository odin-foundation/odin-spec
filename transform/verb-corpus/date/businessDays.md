# %businessDays

add weekday-only days

**Signature:** `%businessDays <date> <integer:count> -> string`

Shift a date by N business days, skipping Saturdays and Sundays.

**Transform**

```odin
{out}
d = %businessDays @.start ##5
overWeekend = %businessDays @.friday ##1
back = %businessDays @.friday ##-5
fromWeekend = %businessDays @.saturday ##1
```

**In**

```odin
start = 2024-01-15
friday = 2024-01-19
saturday = 2024-01-20
```

**Out**

```odin
{out}
d = "2024-01-22"
overWeekend = "2024-01-22"
back = "2024-01-12"
fromWeekend = "2024-01-22"
```

**Notes**

- Weekends are skipped; there is no holiday calendar.
- A negative count subtracts business days.
