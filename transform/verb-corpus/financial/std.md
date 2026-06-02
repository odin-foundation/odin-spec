# %std

population standard deviation

**Signature:** `%std <array:values> -> number`

Compute the population standard deviation of a numeric array.

**Transform**

```odin
{out}
r = %std @.returns
flat = %std @.flat
scalar = %std @.x
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
#3
#3
#3
```

**Out**

```odin
{out}
r = #0.02
flat = ##0
scalar = ~
```

**Notes**

- Divides the summed squared deviations by n (population), not n-1.
- Identical values have zero spread, so the result is 0.
- Provide the array as a typed array block ({returns[] : ~}); a scalar argument yields ~ (null).
