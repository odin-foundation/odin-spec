# %weightedAvg

weighted average of two arrays

**Signature:** `%weightedAvg <array:values> <array:weights> -> number`

Average values weighted by a parallel array of weights.

**Transform**

```odin
{out}
r = %weightedAvg @.values @.weights
zeroWeights = %weightedAvg @.values @.zeros
mismatch = %weightedAvg @.values @.short
```

**In**

```odin
{values[] : ~}
#90
#80
#70

{weights[] : ~}
#3
#2
#1

{zeros[] : ~}
#0
#0
#0

{short[] : ~}
#1
#1
```

**Out**

```odin
{out}
r = #83.33333333333333
zeroWeights = ~
mismatch = ##85
```

**Notes**

- Computes sum(value x weight) / sum(weight); here (90·3 + 80·2 + 70·1) / 6.
- First argument is the values array, second is the weights array - order matters.
- Weights summing to zero make the divisor zero, so the result is ~ (null).
- Arrays of unequal length are truncated to the shorter one; here values[90,80,70] with weights[1,1] averages only the first two to 85.

**Avoid**

- `r = %weightedAvg @.weights @.values`: swapping values and weights changes the result (here to ~2.083); the values array must come first
