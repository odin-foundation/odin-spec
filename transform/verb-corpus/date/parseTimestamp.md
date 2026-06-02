# %parseTimestamp

parse a string into an ISO timestamp

**Signature:** `%parseTimestamp <string:value> <string:pattern> -> string`

Read a timestamp from a formatted string and normalize it to ISO date-time.

**Transform**

```odin
{out}
t = %parseTimestamp @.raw "YYYY-MM-DD HH:mm:ss"
```

**In**

```odin
raw = "2024-06-15 14:30:45"
```

**Out**

```odin
{out}
t = "2024-06-15T14:30:45"
```

**Notes**

- Each component is located by its position in the pattern.
- Output is an ISO date-time string without a zone suffix.
