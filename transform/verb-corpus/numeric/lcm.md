# %lcm

least common multiple

**Signature:** `%lcm <integer> <integer> -> integer`

Compute the least common multiple of two integers.

**Transform**

```odin
{out}
normal = %lcm @.a @.b
withZero = %lcm @.zero @.a
```

**In**

```odin
a = ##4
b = ##6
zero = ##0
```

**Out**

```odin
{out}
normal = ##12
withZero = ##0
```

**Notes**

- lcm(n, 0) is 0. A result beyond the safe integer range yields ~.
