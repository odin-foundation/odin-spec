# %max

largest numeric value in an array

**Signature:** `%max <array> -> number`

Reduce an array reference to its maximum numeric element.

**Transform**

```odin
{out}
hi = %max @.amounts
negatives = %max @.deltas
fractional = %max @.prices
single = %max @.one
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
hi = ##20
negatives = ##-3
fractional = #2.75
single = ##42
```

**Notes**

- Folds the whole array; it is not the scalar comparator (that is %maxOf in the numeric family).
- A whole-number maximum is emitted as an integer (##); a fractional one stays a number (#).
- An empty array yields ~ (null).

**Avoid**

- `hi = %max @.x (where x is a scalar)`: %max reduces an array; a scalar reference resolves to no array and returns ~ instead of the value
