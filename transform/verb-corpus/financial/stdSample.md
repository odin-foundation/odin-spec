# %stdSample

sample standard deviation

**Signature:** `%stdSample <array:values> -> number`

Compute the sample standard deviation (n−1 denominator) of a numeric array.

**Transform**

```odin
{out}
r = %stdSample @.returns
pair = %stdSample @.pair
single = %stdSample @.one
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
r = #0.0223606797749979
pair = #7.0710678118654755
single = ~
```

**Notes**

- Divides the summed squared deviations by n−1 (Bessel's correction), so it exceeds %std on the same data.
- A two-element array [10, 20] gives sqrt(50) = ~7.071; a single-element array yields ~ (null).
