# %addYears

shift a date by whole years

**Signature:** `%addYears <date> <integer:years> -> string`

Add (or subtract) a number of years to a date, returning an ISO date.

**Transform**

```odin
{out}
d = %addYears @.start ##1
```

**In**

```odin
start = 2024-02-29
```

**Out**

```odin
{out}
d = "2025-03-01"
```

**Notes**

- A leap-day input rolls forward to March 1 in a non-leap target year.
- A negative count moves the date backward.
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.
