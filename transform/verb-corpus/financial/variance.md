# %variance

population variance

**Signature:** `%variance <array:values> -> number`

Compute the population variance of a numeric array.

**Transform**

```odin
{out}
r = %variance @.returns
flat = %variance @.flat
scalar = %variance @.x
```

**In**

```odin
x = #5

{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01

{flat[] : ~}
#7
#7
#7
```

**Out**

```odin
{out}
r = #0.0004000000000000001
flat = ##0
scalar = ~
```

**Notes**

- Divides the summed squared deviations by n (population); it is the square of %std.
- The full-precision float, including floating-point noise, is preserved in the output.
- Identical values have zero spread, so the result is 0; a scalar argument yields ~ (null).
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.
