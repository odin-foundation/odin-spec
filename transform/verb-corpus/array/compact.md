# %compact

drop empty values

**Signature:** `%compact <array> -> array`

Remove null and empty-string elements from an array.

**Transform**

```odin
{out}
clean = %compact @.values
```

**In**

```odin
{values[] : ~}
##1
~
##2
```

**Out**

```odin
{out}
{.clean[] : ~}
##1
##2
```

**Notes**

- Removes null (~) and empty-string elements; zero and false are kept.
