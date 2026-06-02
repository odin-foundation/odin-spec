# %endOfDay

raise a timestamp to the last instant of its day

**Signature:** `%endOfDay <timestamp> -> string`

Set a timestamp to the final moment of its day (23:59:59.999).

**Transform**

```odin
{out}
t = %endOfDay @.ts
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "2024-06-15T23:59:59.999Z"
```

**Notes**

- Day boundaries are computed in UTC.
- The result carries 999 milliseconds.
- Output uses the TS Date format (millis/Z or JS year-rollover); verified in TS only.
