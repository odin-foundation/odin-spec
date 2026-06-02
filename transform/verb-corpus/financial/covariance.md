# %covariance

population covariance of two series

**Signature:** `%covariance <array:x> <array:y> -> number`

Measure how two numeric series vary together.

**Transform**

```odin
{out}
r = %covariance @.x @.y
```

**In**

```odin
{x[] : ~}
##1
##2
##3
##4
##5

{y[] : ~}
##2
##4
##5
##4
##6
```

**Out**

```odin
{out}
r = #1.6
```

**Notes**

- Uses the population convention (divides by n) over the overlapping length of the two arrays.
- Both arguments must be arrays; if lengths differ the shorter length is used.
