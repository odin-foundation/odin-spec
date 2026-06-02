# %min

smallest numeric value in an array

**Signature:** `%min <array> -> number`

Reduce an array reference to its minimum numeric element.

**Transform**

```odin
{out}
lo = %min @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##5
```

**Out**

```odin
{out}
lo = ##5
```

**Notes**

- Folds the whole array; it is not the scalar comparator (that is %minOf in the numeric family).
- A whole-number minimum is emitted as an integer (##); a fractional one stays a number (#).
- An empty array yields ~ (null).

**Avoid**

- `lo = %min @.x (where x is a scalar)`: %min reduces an array; a scalar reference resolves to no array and returns ~ instead of the value
