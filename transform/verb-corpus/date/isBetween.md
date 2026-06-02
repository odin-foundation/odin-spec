# %isBetween

inclusive range test

**Signature:** `%isBetween <date> <date:start> <date:end> -> boolean`

Test whether a date falls within a start/end range, inclusive of both ends.

**Transform**

```odin
{out}
inside = %isBetween @.d @.start @.end
atStart = %isBetween @.start @.start @.end
before = %isBetween @.early @.start @.end
```

**In**

```odin
d = 2024-06-15
start = 2024-01-01
end = 2024-12-31
early = 2023-06-15
```

**Out**

```odin
{out}
inside = ?true
atStart = ?true
before = ?false
```

**Notes**

- Both bounds are inclusive - a date equal to start or end returns true.
- Requires all three date arguments.

**Avoid**

- `b = %isBetween @.d @.start`: the end bound is required; only two dates raises a verb-arity error
