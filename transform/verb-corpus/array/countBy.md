# %countBy

count items per field value

**Signature:** `%countBy <array> [<field>] -> object`

Group array items by a field and count how many fall in each group.

**Transform**

```odin
{out}
byRegion = %countBy @.items "region"
```

**In**

```odin
{items[] : region}
"east"
"west"
"east"
```

**Out**

```odin
{out}
{.byRegion}
east = ##2
west = ##1
```

**Notes**

- Without a field argument the items themselves are counted. Result keys are sorted.
