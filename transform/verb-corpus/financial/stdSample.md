# %stdSample

sample standard deviation

**Signature:** `%stdSample <array:values> -> number`

Compute the sample standard deviation (n−1 denominator) of a numeric array.

**Transform**

```odin
{out}
r = %stdSample @.returns
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
r = #0.0223606797749979
```

**Notes**

- Divides the summed squared deviations by n−1 (Bessel's correction), so it exceeds %std on the same data.
- Requires at least two elements; a shorter array yields ~ (null).
