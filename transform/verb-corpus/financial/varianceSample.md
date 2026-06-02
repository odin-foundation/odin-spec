# %varianceSample

sample variance

**Signature:** `%varianceSample <array:values> -> number`

Compute the sample variance (n-1 denominator) of a numeric array.

**Transform**

```odin
{out}
r = %varianceSample @.returns
pair = %varianceSample @.pair
single = %varianceSample @.one
```

**In**

```odin
{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01

{pair[] : ~}
#10
#20

{one[] : ~}
#5
```

**Out**

```odin
{out}
r = #0.0005000000000000001
pair = ##50
single = ~
```

**Notes**

- Divides the summed squared deviations by n-1 (Bessel's correction); it is the square of %stdSample.
- A two-element array [10, 20] gives 50; a single-element array yields ~ (null).
