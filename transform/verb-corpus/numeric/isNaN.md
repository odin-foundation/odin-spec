# %isNaN

NaN test

**Signature:** `%isNaN <number> -> boolean`

Test whether a numeric value is NaN.

**Transform**

```odin
{out}
v = %isNaN @.a
```

**In**

```odin
a = #3.5
```

**Out**

```odin
{out}
v = ?false
```

**Notes**

- Returns a boolean (?).
- Non-numeric input types yield false rather than an error.
