# %percentile

Nth percentile (0-100)

**Signature:** `%percentile <array:values> <number:pct> -> number`

Return the value at a given percentile of a numeric array using linear interpolation.

**Transform**

```odin
{out}
r = %percentile @.values @.p
```

**In**

```odin
{values[] : ~}
##10
##20
##30
##40
##50

p = ##90
```

**Out**

```odin
{out}
r = ##46
```

**Notes**

- The percentile is expressed on a 0-100 scale; 90 means the 90th percentile.
- When the rank falls between two points the result is linearly interpolated.

**Avoid**

- `r = %percentile @.values @.p ; p = #0.9`: %percentile expects 0-100, so 0.9 is read as the 0.9th percentile and returns a value near the minimum, not the 90th percentile; use %quantile for a 0-1 scale
