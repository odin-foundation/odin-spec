# %and

boolean AND

**Signature:** `%and <a:boolean> <b:boolean> -> boolean`

Return true only when both conditions are truthy.

**Transform**

```odin
{out}
both = %and %gte @.age ##18 %lt @.age ##65
failLow = %and %gte @.young ##18 %lt @.young ##65
failHigh = %and %gte @.old ##18 %lt @.old ##65
```

**In**

```odin
age = ##30
young = ##16
old = ##70
```

**Out**

```odin
{out}
both = ?true
failLow = ?false
failHigh = ?false
```

**Notes**

- Each operand is itself a verb-expression that yields a boolean (%gte …, %lt …).
- Exactly two operands; nest %and inside %and for three or more conditions.

**Avoid**

- `both = %and "@.a" "@.b"`: quoted operands are plain strings, and a string is only truthy when it spells "true"/"yes"/"y"/"1"; an arbitrary string like "@.a" is falsy, so this returns ?false regardless of @.a. Pass real boolean verb-expressions, not quoted paths.
