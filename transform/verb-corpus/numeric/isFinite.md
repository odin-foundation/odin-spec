# %isFinite

finite-number test

**Signature:** `%isFinite <number> -> boolean`

Test whether a numeric value is finite (not Infinity or NaN).

**Transform**

```odin
{out}
v = %isFinite @.a
```

**In**

```odin
a = #3.5
```

**Out**

```odin
{out}
v = ?true
```

**Notes**

- Returns a boolean (?).
- Non-numeric input types yield false rather than an error.
