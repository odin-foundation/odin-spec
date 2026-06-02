# %isFinite

finite-number test

**Signature:** `%isFinite <number> -> boolean`

Test whether a numeric value is finite (not Infinity or NaN).

**Transform**

```odin
{out}
v = %isFinite @.a
integer = %isFinite @.i
nonNumeric = %isFinite @.s
```

**In**

```odin
a = #3.5
i = ##42
s = "hello"
```

**Out**

```odin
{out}
v = ?true
integer = ?true
nonNumeric = ?false
```

**Notes**

- Returns a boolean (?).
- Non-numeric input types yield false rather than an error.
