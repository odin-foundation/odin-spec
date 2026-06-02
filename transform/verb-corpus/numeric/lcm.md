# %lcm

least common multiple

**Signature:** `%lcm <integer> <integer> -> integer`

Compute the least common multiple of two integers.

**Transform**

```odin
{out}
l = %lcm @.a @.b
```

**In**

```odin
a = ##4
b = ##6
```

**Out**

```odin
{out}
l = ##12
```

**Notes**

- lcm(n, 0) is 0. A result beyond the safe integer range yields ~.
