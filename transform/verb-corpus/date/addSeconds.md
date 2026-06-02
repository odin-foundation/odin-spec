# %addSeconds

shift a timestamp by seconds

**Signature:** `%addSeconds <timestamp> <number:seconds> -> string`

Add (or subtract) seconds to a timestamp, returning an ISO date-time.

**Transform**

```odin
{out}
t = %addSeconds @.ts ##45
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
```

**Out**

```odin
{out}
t = "2024-06-15T10:30:45.000Z"
```

**Notes**

- Output is a full ISO timestamp with milliseconds and a UTC suffix.
- A negative count moves the timestamp earlier.
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.
