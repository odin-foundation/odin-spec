# %shift

offset elements by N positions

**Signature:** `%shift <array> [<integer:periods>] [<value:fill>] -> array`

Move elements forward (or backward) filling gaps with null.

**Transform**

```odin
{out}
lagged = %shift @.prices ##1
backward = %shift @.prices ##-1
filled = %shift @.prices ##1 ##0
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
{.lagged[] : ~}
~
##10
##20
{.backward[] : ~}
##20
##30
~
{.filled[] : ~}
##0
##10
##20
```

**Notes**

- Positive periods shift toward later indices; leading gaps become null (~).
- A negative period shifts backward; an optional third argument sets the fill value.
