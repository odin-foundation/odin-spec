# %isLeapYear

leap-year test

**Signature:** `%isLeapYear <date> -> boolean`

Report whether the year of a date is a leap year.

**Transform**

```odin
{out}
b = %isLeapYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Applies the full Gregorian rule (divisible by 4, except centuries not divisible by 400).
- 2024 is a leap year.
