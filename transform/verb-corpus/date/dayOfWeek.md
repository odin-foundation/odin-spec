# %dayOfWeek

ISO weekday number

**Signature:** `%dayOfWeek <date> -> integer`

Return the ISO 8601 weekday of a date (Monday=1 through Sunday=7).

**Transform**

```odin
{out}
n = %dayOfWeek @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
n = ##6
```

**Notes**

- Uses ISO numbering: Monday is 1 and Sunday is 7.
- June 15, 2024 is a Saturday, hence 6.
