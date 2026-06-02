# %buildQuery

serialize an object to a query string

**Signature:** `%buildQuery <object> -> string`

Serialize an object into a query string with keys sorted; null values are skipped.

**Transform**

```odin
{out}
qs = %buildQuery @.params
```

**In**

```odin
{params}
z = ##1
a = ##2
```

**Out**

```odin
{out}
qs = "a=2&z=1"
```

**Notes**

- Keys are sorted so the output is canonical regardless of source key order.
- Values are coerced to strings; ~ values are omitted.
