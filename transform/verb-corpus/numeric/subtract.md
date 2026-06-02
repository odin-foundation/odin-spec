# %subtract

difference of two numbers

**Signature:** `%subtract <number:a> <number:b> -> number`

Subtract the second number from the first.

**Transform**

```odin
{out}
v = %subtract @.a @.b
fractional = %subtract @.c @.d
negativeResult = %subtract @.e @.f
```

**In**

```odin
a = #10
b = #3
c = #5.5
d = #2
e = #3
f = #8
```

**Out**

```odin
{out}
v = ##7
fractional = #3.5
negativeResult = ##-5
```

**Notes**

- Argument order is a - b.
- A whole-valued result emits as an integer (##); a fractional result emits as a number (#).

**Avoid**

- `v = %subtract @.a`: two operands are required; one operand raises a verb-arity error
