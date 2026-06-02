# %compact

drop empty values

**Signature:** `%compact <array> -> array`

Remove null and empty-string elements from an array.

**Transform**

```odin
{out}
clean = %compact @.values
keepsFalsy = %compact @.falsy
nonArray = %compact @.notArr
```

**In**

```odin
notArr = "x"

{values[] : ~}
##1
~
##2

{falsy[] : ~}
##0
?false
~
```

**Out**

```odin
{out}
{.clean[] : ~}
##1
##2
{.keepsFalsy[] : ~}
##0
?false
nonArray = ~
```

**Notes**

- Removes null (~) and empty-string elements; zero and false are kept.
