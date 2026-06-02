# %toArray

wrap a value in an array

**Signature:** `%toArray <value> -> array`

Normalize a scalar (or existing array) into an array so downstream array verbs always have a collection.

**Transform**

```odin
{out}
arr = %toArray @.raw
fromNum = %toArray @.num
```

**In**

```odin
raw = "single"
num = ##7
```

**Out**

```odin
{out}
{.arr[] : ~}
"single"
{.fromNum[] : ~}
##7
```

**Notes**

- A scalar becomes a single-element array, rendered as an array sub-block headed by {out.arr[] : ~}.
- A value that is already an array passes through unchanged.
- A null input becomes an empty array, which the serializer elides entirely (no output line).

**Avoid**

- `arr = %toArray @.raw ; input ~ then expecting an empty [] line`: a null coerces to an empty array that the serializer omits, so no array block appears at all
