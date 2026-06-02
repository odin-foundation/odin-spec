# %clamp

constrain a value to a range

**Signature:** `%clamp <number:value> <number:min> <number:max> -> number`

Bound a value between a minimum and a maximum.

**Transform**

```odin
{out}
r = %clamp @.value @.min @.max
```

**In**

```odin
value = #150
min = #0
max = #100
```

**Out**

```odin
{out}
r = ##100
```

**Notes**

- A value above max is pulled down to max; a value below min is raised to min.
- Arguments are value, then min, then max, in that order.

**Avoid**

- `r = %clamp @.value @.min`: %clamp requires value, min, and max; fewer than three arguments raises a verb-arity error
