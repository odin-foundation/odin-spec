# %formatTimestamp

render a full timestamp with a pattern

**Signature:** `%formatTimestamp <timestamp> <string:pattern> -> string`

Format both date and time portions of a timestamp using a display pattern.

**Transform**

```odin
{out}
t = %formatTimestamp @.ts "YYYY-MM-DD HH:mm:ss"
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "2024-06-15 14:30:45"
```

**Notes**

- Combines date tokens (YYYY, MM, DD) and time tokens (HH, mm, ss) in one pattern.
- A timestamp without a zone offset is interpreted as UTC.
