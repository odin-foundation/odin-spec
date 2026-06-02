# %addMonths

shift a date by whole months

**Signature:** `%addMonths <date> <integer:months> -> string`

Add (or subtract) a number of months to a date, returning an ISO date.

**Transform**

```odin
{out}
d = %addMonths @.start ##2
```

**In**

```odin
start = 2024-01-31
```

**Out**

```odin
{out}
d = "2024-03-31"
```

**Notes**

- Month arithmetic uses calendar-month stepping, not a fixed day count.
- A negative count moves the date backward.
