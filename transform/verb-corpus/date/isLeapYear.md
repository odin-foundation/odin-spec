# %isLeapYear

leap-year test

**Signature:** `%isLeapYear <date> -> boolean`

Report whether the year of a date is a leap year.

**Transform**

```odin
{out}
leap = %isLeapYear @.leap
common = %isLeapYear @.common
century = %isLeapYear @.century
century400 = %isLeapYear @.century400
```

**In**

```odin
leap = 2024-06-15
common = 2023-06-15
century = 1900-01-01
century400 = 2000-01-01
```

**Out**

```odin
{out}
leap = ?true
common = ?false
century = ?false
century400 = ?true
```

**Notes**

- Applies the full Gregorian rule (divisible by 4, except centuries not divisible by 400).
- 2024 is a leap year.
