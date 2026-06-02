# %isValidDate

strict format-and-value check

**Signature:** `%isValidDate <string:value> <string:format> -> boolean`

Validate that a string matches a date format and names a real calendar date.

**Transform**

```odin
{out}
b = %isValidDate @.raw "YYYY-MM-DD"
```

**In**

```odin
raw = "2024-02-29"
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Checks both the literal format and that the day exists - Feb 29 is valid only in leap years.
- Supported formats are YYYY-MM-DD, MM/DD/YYYY, DD/MM/YYYY, and YYYY/MM/DD.
