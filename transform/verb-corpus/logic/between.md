# %between

inclusive range test

**Signature:** `%between <value:number> <min:number> <max:number> -> boolean`

Return true when a value falls within [min, max] inclusive.

**Transform**

```odin
{out}
inRange = %between @.age ##18 ##65
below = %between @.child ##18 ##65
atLower = %between @.edge ##18 ##65
atUpper = %between @.top ##18 ##65
```

**In**

```odin
age = ##42
child = ##10
edge = ##18
top = ##65
```

**Out**

```odin
{out}
inRange = ?true
below = ?false
atLower = ?true
atUpper = ?true
```

**Notes**

- Takes three arguments: value, lower bound, upper bound - both bounds inclusive.
- Operands are coerced to numbers before comparison.

**Avoid**

- `inRange = %between @.age ##18`: %between requires all three arguments (value, min, max); supplying only two fails with a 'requires 3 arguments' error rather than defaulting the upper bound.
