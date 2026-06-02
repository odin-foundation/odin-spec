# %quantile

quantile (0-1)

**Signature:** `%quantile <array:values> <number:q> -> number`

Return the value at a given quantile of a numeric array using linear interpolation.

**Transform**

```odin
{out}
r = %quantile @.values @.q
```

**In**

```odin
{values[] : ~}
##10
##20
##30
##40
##50

q = #0.25
```

**Out**

```odin
{out}
r = ##20
```

**Notes**

- The quantile is expressed on a 0-1 scale; 0.25 is the first quartile.
- Same interpolation as %percentile but with a 0-1 argument instead of 0-100.

**Avoid**

- `r = %quantile @.values @.q ; q = ##25`: %quantile expects 0-1, so 25 is out of range and yields ~ (null); use %percentile for a 0-100 scale
