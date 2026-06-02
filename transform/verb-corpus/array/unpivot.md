# %unpivot

object to key/value records

**Signature:** `%unpivot <object> <string:keyName> <string:valueName> -> array`

Reshape an object into an array of { key, value } records.

**Transform**

```odin
{out}
rows = %unpivot @.lookup "key" "value"
```

**In**

```odin
{lookup}
a = ##1
b = ##2
```

**Out**

```odin
{out}
{.rows[] : key, value}
"a", ##1
"b", ##2
```

**Notes**

- The inverse of %pivot; iteration follows the object insertion order.
- The two quoted arguments name the key and value fields in each output record.
