# %lag

previous value at each position

**Signature:** `%lag <array> [<integer:periods>] [<value:default>] -> array`

Shift values forward so each row sees the earlier value.

**Transform**

```odin
{out}
prev = %lag @.prices
by2 = %lag @.prices ##2
withDefault = %lag @.prices ##1 ##0
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
{.by2[] : ~}
~
~
##10
{.withDefault[] : ~}
##0
##10
##20
```

**Notes**

- Leading positions use the default (null unless a third argument is given).
- Equivalent to looking back one position by default.
