# %endOfYear

December 31 of the year

**Signature:** `%endOfYear <date> -> string`

Advance a date to the last day of its calendar year.

**Transform**

```odin
{out}
d = %endOfYear @.mid
fromLast = %endOfYear @.last
bad = %endOfYear @.notDate
```

**In**

```odin
mid = 2024-06-15
last = 2024-12-31
notDate = "nope"
```

**Out**

```odin
{out}
d = "2024-12-31"
fromLast = "2024-12-31"
bad = ~
```

**Notes**

- Returns an ISO date string (month and day set to 12-31).
- Year boundaries are computed in UTC.
