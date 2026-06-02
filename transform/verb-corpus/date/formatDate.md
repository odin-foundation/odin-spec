# %formatDate

render a date with a pattern

**Signature:** `%formatDate <date> <string:pattern> -> string`

Format an ISO date string using a display pattern.

**Transform**

```odin
{out}
date = %formatDate @.date "MM/DD/YYYY"
iso = %formatDate @.date "YYYY-MM-DD"
compact = %formatDate @.date "MMDDYYYY"
bad = %formatDate @.notDate "MM/DD/YYYY"
```

**In**

```odin
date = "2024-06-15"
notDate = "nope"
```

**Out**

```odin
{out}
date = "06/15/2024"
iso = "2024-06-15"
compact = "06152024"
bad = ~
```

**Notes**

- The pattern is a quoted literal (tokens MM, DD, YYYY).
- An unparseable input date yields ~ (null) rather than an error.

**Avoid**

- `date = %formatDate @.date`: the pattern argument is required; omitting it raises a verb-arity error
- `date = %formatDate @.date MM/DD/YYYY`: the pattern must be quoted; an unquoted pattern is not a single string token
