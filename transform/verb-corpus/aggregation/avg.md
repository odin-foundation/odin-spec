# %avg

arithmetic mean of an array

**Signature:** `%avg <array> -> number`

Reduce an array reference to the mean of its numeric elements.

**Transform**

```odin
{out}
mean = %avg @.amounts
fractional = %avg @.thirds
negatives = %avg @.deltas
single = %avg @.one
```

**In**

```odin
{amounts[] : ~}
##10
##20
##30

{thirds[] : ~}
##1
##2
##2

{deltas[] : ~}
##-10
##20
##-40

{one[] : ~}
##42
```

**Out**

```odin
{out}
mean = ##20
fractional = #1.6666666666666667
negatives = ##-10
single = ##42
```

**Notes**

- Sum divided by element count; a whole-number mean is emitted as an integer (##), otherwise as a number (#).
- An empty-but-present array (a single ~ element) averages to ##0; a missing path yields ~.

**Avoid**

- `mean = %avg @.nope (path does not resolve to an array)`: no array resolves, so %avg returns ~ rather than a zero or an error
