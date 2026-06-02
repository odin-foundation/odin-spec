# %std

population standard deviation

**Signature:** `%std <array:values> -> number`

Compute the population standard deviation of a numeric array.

**Transform**

```odin
{out}
r = %std @.returns
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
r = #0.02
```

**Notes**

- Divides the summed squared deviations by n (population), not n−1.
- Provide the array as a typed array block ({returns[] : ~}); a scalar argument yields ~ (null).

**Avoid**

- `r = %std @.x ; x = #5`: passing a scalar instead of an array yields ~ (null); %std needs a numeric array
