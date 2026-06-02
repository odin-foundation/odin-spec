# %dateDiff

difference between two dates in a unit

**Signature:** `%dateDiff <date> <date> <string:unit> -> integer`

Measure the gap from the first date to the second in days, months, or years.

**Transform**

```odin
{out}
d = %dateDiff @.a @.b "days"
```

**In**

```odin
a = 2024-01-01
b = 2024-03-01
```

**Out**

```odin
{out}
d = ##60
```

**Notes**

- The unit literal must be one of "days", "months", or "years".
- The result is the second date minus the first, so b before a yields a negative count.

**Avoid**

- `d = %dateDiff @.a @.b "weeks"`: "weeks" is not a supported unit; only days, months, and years are accepted
