# %min

smallest numeric value in an array

**Signature:** `%min <array> -> number`

Reduce an array reference to its minimum numeric element.

**Transform**

```odin
{out}
lo = %min @.amounts
negatives = %min @.deltas
fractional = %min @.prices
single = %min @.one
```

**In**

```odin
{amounts[] : ~}
##10
##20
##5

{deltas[] : ~}
##-10
##-3
##-40

{prices[] : ~}
#1.5
#2.75
#0.5

{one[] : ~}
##42
```

**Out**

```odin
{out}
lo = ##5
negatives = ##-40
fractional = #0.5
single = ##42
```

**Notes**

- Folds the whole array; it is not the scalar comparator (that is %minOf in the numeric family).
- A whole-number minimum is emitted as an integer (##); a fractional one stays a number (#).
- An empty array yields ~ (null).

**Avoid**

- `lo = %min @.x (where x is a scalar)`: %min reduces an array; a scalar reference resolves to no array and returns ~ instead of the value
