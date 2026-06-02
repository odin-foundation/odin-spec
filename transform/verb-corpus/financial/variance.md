# %variance

population variance

**Signature:** `%variance <array:values> -> number`

Compute the population variance of a numeric array.

**Transform**

```odin
{out}
r = %variance @.returns
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
r = #0.0004000000000000001
```

**Notes**

- Divides the summed squared deviations by n (population); it is the square of %std.
- The full-precision float, including floating-point noise, is preserved in the output.
