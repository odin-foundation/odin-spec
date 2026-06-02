# %startOfDay

floor a timestamp to midnight

**Signature:** `%startOfDay <timestamp> -> string`

Reset a timestamp to the very start of its day (00:00:00.000).

**Transform**

```odin
{out}
t = %startOfDay @.ts
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "2024-06-15T00:00:00.000Z"
```

**Notes**

- Day boundaries are computed in UTC.
- Output is a full ISO timestamp.
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.
