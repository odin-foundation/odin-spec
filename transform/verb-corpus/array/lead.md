# %lead

next value at each position

**Signature:** `%lead <array> [<integer:periods>] [<value:default>] -> array`

Shift values backward so each row sees the following value.

**Transform**

```odin
{out}
next = %lead @.prices
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
{.next[] : ~}
##20
##30
~
```

**Notes**

- Trailing positions use the default (null unless a third argument is given).
- Equivalent to looking ahead one position by default.
