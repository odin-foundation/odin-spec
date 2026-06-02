# %formatTime

render the time portion of a timestamp

**Signature:** `%formatTime <timestamp> <string:pattern> -> string`

Format the clock portion of a timestamp using a display pattern.

**Transform**

```odin
{out}
t = %formatTime @.ts "HH:mm:ss"
short = %formatTime @.ts "HH:mm"
midnight = %formatTime @.mid "HH:mm:ss"
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
mid = 2024-06-15T00:00:00Z
```

**Out**

```odin
{out}
t = "14:30:45"
short = "14:30"
midnight = "00:00:00"
```

**Notes**

- HH is 24-hour; use hh with A for a 12-hour clock.
- Time components are read in UTC.
