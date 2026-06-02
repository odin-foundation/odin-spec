# %count

number of elements in an array

**Signature:** `%count <array> -> integer`

Return the element count of an array reference as an integer.

**Transform**

```odin
{out}
n = %count @.amounts
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
n = ##3
```

**Notes**

- Counts elements regardless of their type; the array need not be numeric.
- Reference an object-field array (@.lines) to count records.

**Avoid**

- `n = %count @.x (where x is a scalar)`: a scalar reference is not an array, so %count silently returns ##0 instead of 1 or an error
