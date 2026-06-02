# %log

logarithm with optional base

**Signature:** `%log <number:value> <number:base?> -> number`

Take the logarithm of a value, natural by default or in a given base.

**Transform**

```odin
{out}
r = %log @.x @.b
natural = %log @.e
baseOne = %log @.x @.one
nonPositive = %log @.zero @.b
```

**In**

```odin
x = #8
b = #2
e = #2.718281828459045
one = #1
zero = #0
```

**Out**

```odin
{out}
r = ##3
natural = ##1
baseOne = ~
nonPositive = ~
```

**Notes**

- With a second argument the result is log(value) / log(base); here log base 2 of 8 is 3.
- Omit the base for the natural logarithm.
- A value <= 0, or a base <= 0 or equal to 1, yields ~ (null).
