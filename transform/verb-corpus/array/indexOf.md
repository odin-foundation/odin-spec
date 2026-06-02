# %indexOf

first index of a value

**Signature:** `%indexOf <array> <value> -> integer`

Return the position of a value in an array, or -1 if absent.

**Transform**

```odin
{out}
pos = %indexOf @.nums ##20
absent = %indexOf @.nums ##99
```

**In**

```odin
nums = "[10, 20, 30]"
```

**Out**

```odin
{out}
pos = ##1
absent = ##-1
```

**Notes**

- Indices are zero-based; a missing value yields ##-1.
- The array is given as a JSON-array string so its elements compare as plain values.

**Avoid**

- `{nums[] : ~} ##10 ##20 ##30`: a typed ODIN array block wraps each element, so equality never matches and %indexOf always returns ##-1; supply the array as a JSON-array string literal instead
