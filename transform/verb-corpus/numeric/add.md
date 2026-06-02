# %add

add two numbers

**Signature:** `%add <number> <number> -> number`

Add two numeric values and return the sum.

**Transform**

```odin
{out}
total = %add @.base @.fee
withString = %add @.base @.strFee
withNull = %add @.base @.nul
mixed = %add @.flt @.fee
```

**In**

```odin
base = ##500
fee = ##75
strFee = "25"
nul = ~
flt = #1.5
```

**Out**

```odin
{out}
total = ##575
withString = ##525
withNull = ##500
mixed = #76.5
```

**Notes**

- Both integer inputs produce an integer (##) result.
- Numeric-looking strings are coerced before adding.

**Avoid**

- `total = %add @.base`: %add needs exactly two arguments; one argument raises a verb-arity error
