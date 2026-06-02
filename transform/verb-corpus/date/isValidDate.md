# %isValidDate

strict format-and-value check

**Signature:** `%isValidDate <string:value> <string:format> -> boolean`

Validate that a string matches a date format and names a real calendar date.

**Transform**

```odin
{out}
valid = %isValidDate @.raw "YYYY-MM-DD"
badMonth = %isValidDate @.badMonth "YYYY-MM-DD"
feb29Common = %isValidDate @.feb29 "YYYY-MM-DD"
slash = %isValidDate @.slash "MM/DD/YYYY"
```

**In**

```odin
raw = "2024-02-29"
badMonth = "2024-13-01"
feb29 = "2023-02-29"
slash = "12/31/2024"
```

**Out**

```odin
{out}
valid = ?true
badMonth = ?false
feb29Common = ?false
slash = ?true
```

**Notes**

- Checks both the literal format and that the day exists - Feb 29 is valid only in leap years.
- Supported formats are YYYY-MM-DD, MM/DD/YYYY, DD/MM/YYYY, and YYYY/MM/DD.
