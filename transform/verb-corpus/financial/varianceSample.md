# %varianceSample

sample variance

**Signature:** `%varianceSample <array:values> -> number`

Compute the sample variance (n−1 denominator) of a numeric array.

**Transform**

```odin
{out}
r = %varianceSample @.returns
```

**In**

```odin
{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01
```

**Out**

```odin
{out}
r = #0.0005000000000000001
```

**Notes**

- Divides the summed squared deviations by n−1 (Bessel's correction); it is the square of %stdSample.
- Requires at least two elements; a shorter array yields ~ (null).
