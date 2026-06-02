# %addMonths

shift a date by whole months

**Signature:** `%addMonths <date> <integer:months> -> string`

Add (or subtract) a number of months to a date, returning an ISO date.

**Transform**

```odin
{out}
d = %addMonths @.start ##2
febClamp = %addMonths @.jan31 ##1
back = %addMonths @.start ##-3
bad = %addMonths @.notDate ##2
```

**In**

```odin
start = 2024-01-31
jan31 = 2024-01-31
notDate = "nope"
```

**Out**

```odin
{out}
d = "2024-03-31"
febClamp = "2024-02-29"
back = "2023-10-31"
bad = ~
```

**Notes**

- Month arithmetic uses calendar-month stepping, not a fixed day count.
- A negative count moves the date backward.
- A day absent from the target month is clamped to that month's last day (Jan 31 + 1 month is Feb 29 in a leap year, Feb 28 otherwise), not rolled forward.
