# %multiply

product of two numbers

**Signature:** `%multiply <number:a> <number:b> -> number`

Multiply two numbers.

**Transform**

```odin
{out}
v = %multiply @.a @.b
```

**In**

```odin
a = #6
b = #7
```

**Out**

```odin
{out}
v = ##42
```

**Notes**

- A whole-valued product emits as an integer (##); a fractional product emits as a number (#).

**Avoid**

- `v = %multiply @.a`: two operands are required; one operand raises a verb-arity error
