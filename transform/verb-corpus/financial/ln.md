# %ln

natural logarithm

**Signature:** `%ln <number:value> -> number`

Take the natural logarithm (base e) of a value.

**Transform**

```odin
{out}
r = %ln @.e
one = %ln @.one
zero = %ln @.zero
negative = %ln @.neg
```

**In**

```odin
e = #2.718281828459045
one = #1
zero = #0
neg = #-2
```

**Out**

```odin
{out}
r = ##1
one = ##0
zero = ~
negative = ~
```

**Notes**

- ln(e) is 1; passing Euler's number returns the integer 1.
- A value <= 0 yields ~ (null).
