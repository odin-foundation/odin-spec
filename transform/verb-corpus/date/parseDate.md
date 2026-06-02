# %parseDate

parse a string into an ISO date

**Signature:** `%parseDate <string:value> <string:pattern> -> string`

Read a date from a formatted string and normalize it to YYYY-MM-DD.

**Transform**

```odin
{out}
d = %parseDate @.raw "MM/DD/YYYY"
```

**In**

```odin
raw = "06/15/2024"
```

**Out**

```odin
{out}
d = "2024-06-15"
```

**Notes**

- The pattern locates each component by position (MM, DD, YYYY).
- Output is always an ISO date string regardless of the input pattern.

**Avoid**

- `d = %parseDate @.raw`: the pattern argument is required; omitting it raises a verb-arity error
