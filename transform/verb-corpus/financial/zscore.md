# %zscore

standard score of a value

**Signature:** `%zscore <number:value> <array:dataset> -> number`

Express how many population standard deviations a value lies from a dataset's mean.

**Transform**

```odin
{out}
r = %zscore @.value @.scores
atMean = %zscore @.mean @.scores
flat = %zscore @.value @.flat
```

**In**

```odin
value = ##85
mean = ##80

{scores[] : ~}
##70
##75
##80
##85
##90

{flat[] : ~}
##50
##50
##50
```

**Out**

```odin
{out}
r = #0.7071067811865475
atMean = ##0
flat = ~
```

**Notes**

- Uses the population standard deviation of the dataset; here mean 80, std ~7.07, so 85 is ~0.71 std above.
- Argument order is the value first, then the dataset array.
- A value equal to the mean has a z-score of 0.
- If every dataset value is identical the standard deviation is 0 and the result is ~ (null).
