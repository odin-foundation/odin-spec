# %fromUnix

epoch seconds to ISO timestamp

**Signature:** `%fromUnix <integer:epoch> -> string`

Convert a Unix epoch value to an ISO timestamp string.

**Transform**

```odin
{out}
t = %fromUnix @.epoch
```

**In**

```odin
epoch = ##1718447400
```

**Out**

```odin
{out}
t = "2024-06-15T10:30:00.000Z"
```

**Notes**

- Seconds and milliseconds are auto-detected by magnitude.
- Output is a full ISO timestamp with a UTC suffix.
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.
