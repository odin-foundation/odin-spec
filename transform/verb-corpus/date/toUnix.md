# %toUnix

timestamp to epoch seconds

**Signature:** `%toUnix <timestamp> -> integer`

Convert a timestamp to whole seconds since the Unix epoch.

**Transform**

```odin
{out}
ts = %toUnix @.ts
epoch = %toUnix @.epoch
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
epoch = 1970-01-01T00:00:00Z
```

**Out**

```odin
{out}
ts = ##1718447400
epoch = ##0
```

**Notes**

- Result is seconds (not milliseconds) since 1970-01-01T00:00:00Z, floored.
- Pairs with %fromUnix for the reverse conversion.
