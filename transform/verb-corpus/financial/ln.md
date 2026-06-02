# %ln

natural logarithm

**Signature:** `%ln <number:value> -> number`

Take the natural logarithm (base e) of a value.

**Transform**

```odin
{out}
r = %ln @.x
```

**In**

```odin
x = #2.718281828459045
```

**Out**

```odin
{out}
r = ##1
```

**Notes**

- ln(e) is 1; passing Euler's number returns the integer 1.
- A value <= 0 yields ~ (null).
