# %weightedAvg

weighted average of two arrays

**Signature:** `%weightedAvg <array:values> <array:weights> -> number`

Average values weighted by a parallel array of weights.

**Transform**

```odin
{out}
r = %weightedAvg @.values @.weights
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
```

**Out**

```odin
{out}
r = #83.33333333333333
```

**Notes**

- Computes sum(value × weight) / sum(weight); here (90·3 + 80·2 + 70·1) / 6.
- First argument is the values array, second is the weights array - order matters.

**Avoid**

- `r = %weightedAvg @.weights @.values`: swapping values and weights changes the result (here to ~2.083); the values array must come first
