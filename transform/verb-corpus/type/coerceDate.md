# %coerceDate

convert a value to a date

**Signature:** `%coerceDate <value> -> date`

Parse a string (or number/timestamp) into a typed date, dropping any time component.

**Transform**

```odin
{out}
d = %coerceDate @.raw
```

**In**

```odin
raw = "2024-06-15"
```

**Out**

```odin
{out}
d = 2024-06-15
```

**Notes**

- The quoted string becomes a bare date value (no quotes, no prefix) rendered as YYYY-MM-DD.
- Many input shapes parse: ISO, YYYYMMDD, MM/DD/YYYY, DD.MM.YYYY, written months, and numeric Unix timestamps.
- An unparseable value yields ~ (null) instead of an error.

**Avoid**

- `d = %coerceDate @.raw ; input "not-a-date"`: an unrecognized date string silently becomes ~ (null), never an error
