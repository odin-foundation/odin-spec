# %toObject

build an object from key/value pairs

**Signature:** `%toObject <array> -> object`

Fold an array of [key, value] pairs into a single object keyed by the first element.

**Transform**

```odin
{out}
obj = %toObject @.pairs
```

**In**

```odin
pairs[0][0] = "a"
pairs[0][1] = ##1
pairs[1][0] = "b"
pairs[1][1] = ##2
```

**Out**

```odin
{out}
{.obj}
a = ##1
b = ##2
```

**Notes**

- Each [key, value] pair becomes a field, rendered as the {.obj} sub-block.
- Pairs may also be {key, value} or {k, v} objects; the key is stringified.
- A non-array input yields ~ (null).

**Avoid**

- `obj = %toObject @.raw ; input is a plain string`: toObject only accepts arrays; any non-array input silently produces ~ (null)
