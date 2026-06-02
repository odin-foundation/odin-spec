# %addHours

shift a timestamp by hours

**Signature:** `%addHours <timestamp> <number:hours> -> string`

Add (or subtract) hours to a timestamp, returning an ISO date-time.

**Transform**

```odin
{out}
t = %addHours @.ts ##5
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
```

**Out**

```odin
{out}
t = "2024-06-15T15:30:00.000Z"
```

**Notes**

- Output is a full ISO timestamp with milliseconds and a UTC suffix.
- A negative count moves the timestamp earlier.
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.
