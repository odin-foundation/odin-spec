# %sortDesc

sort a primitive array descending

**Signature:** `%sortDesc <array> -> array`

Order array values from highest to lowest.

**Transform**

```odin
{out}
sorted = %sortDesc @.scores
```

**In**

```odin
{scores[] : ~}
##10
##30
##20
```

**Out**

```odin
{out}
{.sorted[] : ~}
##30
##20
##10
```

**Notes**

- Takes only the array - no field argument; it sorts the values directly.
