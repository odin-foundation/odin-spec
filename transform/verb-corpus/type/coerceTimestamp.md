# %coerceTimestamp

convert a value to a timestamp

**Signature:** `%coerceTimestamp <value> -> timestamp`

Parse a value into a typed timestamp, preserving the time-of-day component.

**Transform**

```odin
{out}
t = %coerceTimestamp @.raw
```

**In**

```odin
raw = "2024-06-15T09:30:00Z"
```

**Out**

```odin
{out}
t = 2024-06-15T09:30:00.000Z
```

**Notes**

- Unlike %coerceDate, the time component survives and the value renders as a full ISO 8601 timestamp.
- The output is normalized to UTC with millisecond precision (.000Z).
- An unparseable value yields ~ (null).
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.

**Avoid**

- `t = %coerceDate @.raw ; when you need the time-of-day`: coerceDate discards the time and renders only YYYY-MM-DD; use coerceTimestamp to keep HH:MM:SS
