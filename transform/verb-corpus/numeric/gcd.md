# %gcd

greatest common divisor

**Signature:** `%gcd <integer> <integer> -> integer`

Compute the greatest common divisor of two integers.

**Transform**

```odin
{out}
g = %gcd @.a @.b
```

**In**

```odin
a = ##12
b = ##18
```

**Out**

```odin
{out}
g = ##6
```

**Notes**

- Operands are truncated to their absolute integer value; gcd(n, 0) is n.
