# %add

add two numbers

**Signature:** `%add <number> <number> -> number`

Add two numeric values and return the sum.

**Transform**

```odin
{out}
total = %add @.base @.fee
```

**In**

```odin
base = ##500
fee = ##75
```

**Out**

```odin
{out}
total = ##575
```

**Notes**

- Both integer inputs produce an integer (##) result.
- Numeric-looking strings are coerced before adding.

**Avoid**

- `total = %add @.base`: %add needs exactly two arguments; one argument raises a verb-arity error
