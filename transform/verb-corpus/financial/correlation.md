# %correlation

Pearson correlation coefficient

**Signature:** `%correlation <array:x> <array:y> -> number`

Measure the linear correlation between two numeric series, from -1 to 1.

**Transform**

```odin
{out}
r = %correlation @.x @.y
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
r = #0.8528028654224417
```

**Notes**

- Result lies in [-1, 1]; positive means the series move together.
- If either series has zero variance the coefficient is undefined and yields ~ (null).
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

**Avoid**

- `r = %correlation @.x`: %correlation requires two array arguments; one argument raises a verb-arity error
