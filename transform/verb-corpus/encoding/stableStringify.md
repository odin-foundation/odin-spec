# %stableStringify

canonical JSON

**Signature:** `%stableStringify <any> -> string`

Serialize a value to JSON with object keys sorted recursively, for a stable representation.

**Transform**

```odin
{out}
s = %stableStringify @.doc
```

**In**

```odin
{doc}
b = ##2
a = ##1
```

**Out**

```odin
{out}
s = "{\"a\":1,\"b\":2}"
```

**Notes**

- Object keys are sorted at every level, so the output is independent of source key order.
- Pair with %canonicalHash to fingerprint a value.
