# %factorial

n!

**Signature:** `%factorial <integer> -> integer`

Compute the factorial of a non-negative integer.

**Transform**

```odin
{out}
five = %factorial @.five
zero = %factorial @.zero
max = %factorial @.max
over = %factorial @.over
negative = %factorial @.neg
```

**In**

```odin
five = ##5
zero = ##0
max = ##18
over = ##19
neg = ##-1
```

**Out**

```odin
{out}
five = ##120
zero = ##1
max = ##6402373705728000
over = ~
negative = ~
```

**Notes**

- Defined for 0 <= n <= 18, which is the range that stays within safe integer precision; other inputs yield ~.
