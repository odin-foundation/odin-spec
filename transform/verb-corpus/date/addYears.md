# %addYears

shift a date by whole years

**Signature:** `%addYears <date> <integer:years> -> string`

Add (or subtract) a number of years to a date, returning an ISO date.

**Transform**

```odin
{out}
d = %addYears @.start ##2
febClamp = %addYears @.leap ##1
```

**In**

```odin
start = 2022-06-15
leap = 2024-02-29
```

**Out**

```odin
{out}
d = "2024-06-15"
febClamp = "2025-02-28"
```

**Notes**

- A negative count moves the date backward.
- Feb 29 is clamped to Feb 28 when the target year is not a leap year, not rolled into March.
