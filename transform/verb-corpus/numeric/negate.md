# %negate

arithmetic negation

**Signature:** `%negate <number> -> number`

Return the additive inverse of a number.

**Transform**

```odin
{out}
v = %negate @.a
ofNegative = %negate @.b
ofZero = %negate @.c
```

**In**

```odin
a = #8
b = #-12
c = #0
```

**Out**

```odin
{out}
v = ##-8
ofNegative = ##12
ofZero = ##0
```

**Notes**

- A whole-valued result emits as an integer (##).

**Avoid**

- `v = %negate`: the value argument is required; omitting it raises a verb-arity error
