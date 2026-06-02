# %gcd

greatest common divisor

**Signature:** `%gcd <integer> <integer> -> integer`

Compute the greatest common divisor of two integers.

**Transform**

```odin
{out}
normal = %gcd @.a @.b
withZero = %gcd @.zero @.a
negative = %gcd @.neg @.b
```

**In**

```odin
a = ##12
b = ##18
zero = ##0
neg = ##-12
```

**Out**

```odin
{out}
normal = ##6
withZero = ##12
negative = ##6
```

**Notes**

- Operands are truncated to their absolute integer value; gcd(n, 0) is n.
