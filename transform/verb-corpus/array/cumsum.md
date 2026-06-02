# %cumsum

running total

**Signature:** `%cumsum <array> -> array`

Return the cumulative sum at each position.

**Transform**

```odin
{out}
running = %cumsum @.values
withNeg = %cumsum @.withNeg
nonArray = %cumsum @.notArr
```

**In**

```odin
notArr = "x"

{values[] : ~}
##10
##20
##30

{withNeg[] : ~}
##5
##-2
##-10
```

**Out**

```odin
{out}
{.running[] : ~}
##10
##30
##60
{.withNeg[] : ~}
##5
##3
##-7
nonArray = ~
```

**Notes**

- Each element is the sum of all values up to and including that position.
