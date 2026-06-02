# %formatDuration

human-readable duration

**Signature:** `%formatDuration <integer:seconds> -> string`

Render a duration in seconds (or ISO 8601) as English components.

**Transform**

```odin
{out}
s = %formatDuration @.elapsed
```

**In**

```odin
elapsed = ##90061
```

**Out**

```odin
{out}
s = "1 day, 1 hour, 1 minute, 1 second"
```

**Notes**

- A numeric input is treated as seconds and expanded into days, hours, minutes, and seconds.
- An ISO 8601 duration string such as "PT2H30M" is also accepted; zero components are omitted.
