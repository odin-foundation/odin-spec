# %addDays

shift a date by whole days

**Signature:** `%addDays <date> <integer:days> -> string`

Add (or subtract) a number of days to a date, returning an ISO date.

**Transform**

```odin
{out}
d = %addDays @.start ##7
back = %addDays @.start ##-10
monthRoll = %addDays @.eom ##1
zero = %addDays @.start ##0
bad = %addDays @.notDate ##7
```

**In**

```odin
start = 2024-06-15
eom = 2024-01-31
notDate = "nope"
```

**Out**

```odin
{out}
d = "2024-06-22"
back = "2024-06-05"
monthRoll = "2024-02-01"
zero = "2024-06-15"
bad = ~
```

**Notes**

- The day count is a fractional value floored to a whole number of days.
- A negative count moves the date backward.

**Avoid**

- `d = %addDays @.start "7"`: the count should be a numeric token; a quoted string is coerced but obscures intent
