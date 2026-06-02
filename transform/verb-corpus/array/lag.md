# %lag

previous value at each position

**Signature:** `%lag <array> [<integer:periods>] [<value:default>] -> array`

Shift values forward so each row sees the earlier value.

**Transform**

```odin
{out}
prev = %lag @.prices
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
{.prev[] : ~}
~
##10
##20
```

**Notes**

- Leading positions use the default (null unless a third argument is given).
- Equivalent to looking back one position by default.
