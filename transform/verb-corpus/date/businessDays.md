# %businessDays

add weekday-only days

**Signature:** `%businessDays <date> <integer:count> -> string`

Shift a date by N business days, skipping Saturdays and Sundays.

**Transform**

```odin
{out}
d = %businessDays @.start ##5
```

**In**

```odin
start = 2024-01-15
```

**Out**

```odin
{out}
d = "2024-01-22"
```

**Notes**

- Weekends are skipped; there is no holiday calendar.
- A negative count subtracts business days.
