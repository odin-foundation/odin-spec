# %cumsum

running total

**Signature:** `%cumsum <array> -> array`

Return the cumulative sum at each position.

**Transform**

```odin
{out}
running = %cumsum @.values
```

**In**

```odin
{values[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
{.running[] : ~}
##10
##30
##60
```

**Notes**

- Each element is the sum of all values up to and including that position.
