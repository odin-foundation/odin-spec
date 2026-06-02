# %endOfMonth

last day of the month

**Signature:** `%endOfMonth <date> -> string`

Advance a date to the final day of its calendar month.

**Transform**

```odin
{out}
d = %endOfMonth @.feb
thirty = %endOfMonth @.apr
last = %endOfMonth @.dec
bad = %endOfMonth @.notDate
```

**In**

```odin
feb = 2024-02-15
apr = 2024-04-10
dec = 2024-12-01
notDate = "nope"
```

**Out**

```odin
{out}
d = "2024-02-29"
thirty = "2024-04-30"
last = "2024-12-31"
bad = ~
```

**Notes**

- Leap years are honored - February 2024 ends on the 29th.
- Returns an ISO date string.
