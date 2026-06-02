# %divide

quotient of two numbers

**Signature:** `%divide <number:a> <number:b> -> number`

Divide the first number by the second.

**Transform**

```odin
{out}
v = %divide @.a @.b
byZero = %divide @.a @.zero
exact = %divide @.ten @.two
negative = %divide @.negTen @.two
```

**In**

```odin
a = #7
b = #2
zero = #0
ten = ##10
two = ##2
negTen = ##-10
```

**Out**

```odin
{out}
v = #3.5
byZero = ~
exact = #5
negative = #-5
```

**Notes**

- Argument order is a / b.
- Division always yields a number type (#), reflecting the possibility of a decimal result.
- A zero divisor yields null (~) rather than an error; use %safeDivide to supply a fallback.

**Avoid**

- `v = %divide @.a`: two operands are required; one operand raises a verb-arity error
