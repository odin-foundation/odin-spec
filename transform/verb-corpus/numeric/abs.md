# %abs

absolute value

**Signature:** `%abs <number> -> number`

Return the absolute value of a number.

**Transform**

```odin
{out}
v = %abs @.a
wholeNeg = %abs @.b
fromString = %abs @.c
ofNull = %abs @.d
```

**In**

```odin
a = #-7.25
b = ##-8
c = "-12.5"
d = ~
```

**Out**

```odin
{out}
v = #7.25
wholeNeg = ##8
fromString = #12.5
ofNull = ##0
```

**Notes**

- A fractional input stays a number (#); a whole-valued result would emit as an integer (##).

**Avoid**

- `v = %abs`: the value argument is required; omitting it raises a verb-arity error
