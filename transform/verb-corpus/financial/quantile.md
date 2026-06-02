# %quantile

quantile (0-1)

**Signature:** `%quantile <array:values> <number:q> -> number`

Return the value at a given quantile of a numeric array using linear interpolation.

**Transform**

```odin
{out}
q1 = %quantile @.values @.q
median = %quantile @.values @.qmid
max = %quantile @.values @.qtop
outOfRange = %quantile @.values @.qbad
```

**In**

```odin
q = #0.25
qmid = #0.5
qtop = #1
qbad = #25

{values[] : ~}
##10
##20
##30
##40
##50
```

**Out**

```odin
{out}
q1 = ##20
median = ##30
max = ##50
outOfRange = ~
```

**Notes**

- The quantile is expressed on a 0-1 scale; 0.25 is the first quartile, 0.5 the median, 1 the maximum.
- Same interpolation as %percentile but with a 0-1 argument instead of 0-100.
- A q outside 0-1 (here 25) is out of range and yields ~ (null).

**Avoid**

- `r = %quantile @.values @.q ; q = ##25`: %quantile expects 0-1, so 25 is out of range and yields ~ (null); use %percentile for a 0-100 scale
