# %isNaN

NaN test

**Signature:** `%isNaN <number> -> boolean`

Test whether a numeric value is NaN.

**Transform**

```odin
{out}
v = %isNaN @.a
zero = %isNaN @.z
nonNumeric = %isNaN @.s
```

**In**

```odin
a = #3.5
z = ##0
s = "hello"
```

**Out**

```odin
{out}
v = ?false
zero = ?false
nonNumeric = ?false
```

**Notes**

- Returns a boolean (?).
- Non-numeric input types yield false rather than an error.
