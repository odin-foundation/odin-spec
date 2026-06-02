# %clamp

constrain a value to a range

**Signature:** `%clamp <number:value> <number:min> <number:max> -> number`

Bound a value between a minimum and a maximum.

**Transform**

```odin
{out}
above = %clamp @.high @.min @.max
below = %clamp @.low @.min @.max
inRange = %clamp @.mid @.min @.max
```

**In**

```odin
high = #150
low = #-20
mid = #42
min = #0
max = #100
```

**Out**

```odin
{out}
above = ##100
below = ##0
inRange = ##42
```

**Notes**

- A value above max is pulled down to max; a value below min is raised to min.
- Arguments are value, then min, then max, in that order.

**Avoid**

- `r = %clamp @.value @.min`: %clamp requires value, min, and max; fewer than three arguments raises a verb-arity error
