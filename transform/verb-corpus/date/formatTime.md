# %formatTime

render the time portion of a timestamp

**Signature:** `%formatTime <timestamp> <string:pattern> -> string`

Format the clock portion of a timestamp using a display pattern.

**Transform**

```odin
{out}
t = %formatTime @.ts "HH:mm:ss"
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "14:30:45"
```

**Notes**

- HH is 24-hour; use hh with A for a 12-hour clock.
- Time components are read in UTC.
