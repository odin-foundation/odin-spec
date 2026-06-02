# %mod

remainder of division

**Signature:** `%mod <number:a> <number:b> -> number`

Return the remainder of dividing the first number by the second.

**Transform**

```odin
{out}
v = %mod @.a @.b
negDividend = %mod @.c @.d
zeroDivisor = %mod @.e @.f
```

**In**

```odin
a = #17
b = #5
c = #-17
d = #5
e = #10
f = #0
```

**Out**

```odin
{out}
v = ##2
negDividend = ##-2
zeroDivisor = ~
```

**Notes**

- Argument order is a mod b.
- A zero divisor yields null (~) rather than an error.

**Avoid**

- `v = %mod @.a`: two operands are required; one operand raises a verb-arity error
