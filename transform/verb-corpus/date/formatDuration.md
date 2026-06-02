# %formatDuration

human-readable duration

**Signature:** `%formatDuration <integer:seconds> -> string`

Render a duration in seconds (or ISO 8601) as English components.

**Transform**

```odin
{out}
s = %formatDuration @.elapsed
iso = %formatDuration @.isoDur
oneHour = %formatDuration @.hour
seconds = %formatDuration @.secs
```

**In**

```odin
elapsed = ##90061
isoDur = "PT2H30M"
hour = ##3600
secs = ##45
```

**Out**

```odin
{out}
s = "1 day, 1 hour, 1 minute, 1 second"
iso = "2 hours, 30 minutes"
oneHour = "1 hour"
seconds = "45 seconds"
```

**Notes**

- A numeric input is treated as seconds and expanded into days, hours, minutes, and seconds.
- An ISO 8601 duration string such as "PT2H30M" is also accepted; zero components are omitted.
