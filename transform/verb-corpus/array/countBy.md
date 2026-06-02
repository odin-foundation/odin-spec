# %countBy

count items per field value

**Signature:** `%countBy <array> [<field>] -> object`

Group array items by a field and count how many fall in each group.

**Transform**

```odin
{out}
byRegion = %countBy @.items "region"
byValue = %countBy @.tags
nonArray = %countBy @.notArr
```

**In**

```odin
notArr = "x"

{items[] : region}
"east"
"west"
"east"

{tags[] : ~}
"a"
"b"
"a"
"a"
```

**Out**

```odin
{out}
nonArray = ~
{.byRegion}
east = ##2
west = ##1
{.byValue}
a = ##3
b = ##1
```

**Notes**

- Without a field argument the items themselves are counted. Result keys are sorted.
