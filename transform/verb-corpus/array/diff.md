# %diff

consecutive differences

**Signature:** `%diff <array> [<integer:periods>] -> array`

Return the difference between each element and the prior one.

**Transform**

```odin
{out}
changes = %diff @.values
periods2 = %diff @.values ##2
nonArray = %diff @.notArr
```

**In**

```odin
notArr = "x"

{values[] : ~}
##10
##15
##12
##18
```

**Out**

```odin
{out}
{.changes[] : ~}
~
##5
##-3
##6
{.periods2[] : ~}
~
~
##2
##3
nonArray = ~
```

**Notes**

- The first element is null; later elements are value minus the prior value.
- An optional periods argument compares against N positions back.
