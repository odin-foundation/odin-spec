# %zscore

standard score of a value

**Signature:** `%zscore <number:value> <array:dataset> -> number`

Express how many population standard deviations a value lies from a dataset's mean.

**Transform**

```odin
{out}
r = %zscore @.value @.scores
```

**In**

```odin
value = ##85

{scores[] : ~}
##70
##75
##80
##85
##90
```

**Out**

```odin
{out}
r = #0.7071067811865475
```

**Notes**

- Uses the population standard deviation of the dataset; here mean 80, std ~7.07, so 85 is ~0.71 std above.
- Argument order is the value first, then the dataset array.
- If every dataset value is identical the standard deviation is 0 and the result is ~ (null).
