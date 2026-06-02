# %parseTimestamp

parse a string into an ISO timestamp

**Signature:** `%parseTimestamp <string:value> <string:pattern> -> string`

Read a timestamp from a formatted string and normalize it to ISO date-time.

**Transform**

```odin
{out}
spaced = %parseTimestamp @.raw "YYYY-MM-DD HH:mm:ss"
compact = %parseTimestamp @.compact "YYYYMMDDHHmmss"
```

**In**

```odin
raw = "2024-06-15 14:30:45"
compact = "20240615143045"
```

**Out**

```odin
{out}
spaced = "2024-06-15T14:30:45"
compact = "2024-06-15T14:30:45"
```

**Notes**

- Each component is located by its position in the pattern.
- Output is an ISO date-time string without a zone suffix.
