# %interpolate

linear interpolation between two points

**Signature:** `%interpolate <number:x> <number:x1> <number:y1> <number:x2> <number:y2> -> number`

Estimate y at x along the line through (x1, y1) and (x2, y2).

**Transform**

```odin
{out}
r = %interpolate @.x @.x1 @.y1 @.x2 @.y2
```

**In**

```odin
x = #5
x1 = #0
y1 = #100
x2 = #10
y2 = #200
```

**Out**

```odin
{out}
r = ##150
```

**Notes**

- x = 5 is halfway between x1 = 0 and x2 = 10, so y is halfway between 100 and 200.
- Argument order is x, x1, y1, x2, y2; when x2 equals x1 the result is y1.

**Avoid**

- `r = %interpolate @.x @.x1 @.y1 @.x2`: %interpolate requires all five arguments; fewer raises a verb-arity error
