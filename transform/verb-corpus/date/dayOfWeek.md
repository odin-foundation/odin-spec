# %dayOfWeek

ISO weekday number

**Signature:** `%dayOfWeek <date> -> integer`

Return the ISO 8601 weekday of a date (Monday=1 through Sunday=7).

**Transform**

```odin
{out}
n = %dayOfWeek @.sat
mon = %dayOfWeek @.mon
sun = %dayOfWeek @.sun
bad = %dayOfWeek @.notDate
```

**In**

```odin
sat = 2024-06-15
mon = 2024-06-17
sun = 2024-06-16
notDate = "nope"
```

**Out**

```odin
{out}
n = ##6
mon = ##1
sun = ##7
bad = ~
```

**Notes**

- Uses ISO numbering: Monday is 1 and Sunday is 7.
- June 15, 2024 is a Saturday, hence 6.
