# %sum

total of an array's numeric values

**Signature:** `%sum <array> -> number`

Reduce an array reference to the sum of its numeric elements.

**Transform**

```odin
{out}
total = %sum @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
total = ##60
```

**Notes**

- Takes a single array reference and folds its elements; it is not variadic over scalar arguments.
- A whole-number total is emitted as an integer (##); a fractional total stays a number (#).
- Point the reference at an object-field array (@.lines.amount) to sum one field across records.

**Avoid**

- `total = %sum @.x (where x is a scalar number)`: %sum expects an array; a scalar reference resolves to no array and silently returns #0 rather than the value or an error
