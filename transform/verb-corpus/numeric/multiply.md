# %multiply

product of two numbers

**Signature:** `%multiply <number:a> <number:b> -> number`

Multiply two numbers.

**Transform**

```odin
{out}
v = %multiply @.a @.b
fractional = %multiply @.c @.d
negatives = %multiply @.e @.f
```

**In**

```odin
a = #6
b = #7
c = #2.5
d = #4
e = #-3
f = #-5
```

**Out**

```odin
{out}
v = ##42
fractional = ##10
negatives = ##15
```

**Notes**

- A whole-valued product emits as an integer (##); a fractional product emits as a number (#).

**Avoid**

- `v = %multiply @.a`: two operands are required; one operand raises a verb-arity error
