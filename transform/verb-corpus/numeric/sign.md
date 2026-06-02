# %sign

sign of a number

**Signature:** `%sign <number> -> integer`

Return -1, 0, or 1 according to the sign of a number.

**Transform**

```odin
{out}
v = %sign @.a
```

**In**

```odin
a = #-3.5
```

**Out**

```odin
{out}
v = ##-1
```

**Notes**

- Returns an integer (##): -1 for negatives, 1 for positives, 0 for zero.
