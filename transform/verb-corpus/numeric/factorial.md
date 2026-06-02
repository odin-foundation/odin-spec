# %factorial

n!

**Signature:** `%factorial <integer> -> integer`

Compute the factorial of a non-negative integer.

**Transform**

```odin
{out}
f = %factorial @.n
```

**In**

```odin
n = ##6
```

**Out**

```odin
{out}
f = ##720
```

**Notes**

- Defined for 0 <= n <= 18, which is the range that stays within safe integer precision; other inputs yield ~.
