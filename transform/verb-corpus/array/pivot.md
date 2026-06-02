# %pivot

records to a keyed object

**Signature:** `%pivot <array> <string:keyField> <string:valueField> -> object`

Reshape an array of records into one object keyed by a field.

**Transform**

```odin
{out}
{.lookup} = %pivot @.items "name" "value"
{.dupKeys} = %pivot @.dups "name" "value"
```

**In**

```odin
{items[] : name, value}
"a", ##1
"b", ##2

{dups[] : name, value}
"a", ##1
"a", ##9
```

**Out**

```odin
{out}
dupKeys.a = ##9
{.lookup}
a = ##1
b = ##2
```

**Notes**

- Keys come from keyField and values from valueField; duplicate keys keep the last value.
- The result is an object, emitted as a relative sub-section ({.lookup}).
