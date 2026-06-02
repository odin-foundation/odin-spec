# %stableStringify

canonical JSON

**Signature:** `%stableStringify <any> -> string`

Serialize a value to JSON with object keys sorted recursively, for a stable representation.

**Transform**

```odin
{out}
canonical = %stableStringify @.doc
list = %stableStringify @.arr
scalar = %stableStringify @.n
```

**In**

```odin
n = ##42

{arr[] : ~}
##3
##1
##2

{doc}
b = ##2
a = ##1

{doc.nested}
y = ##2
x = ##1
```

**Out**

```odin
{out}
canonical = "{\"a\":1,\"b\":2,\"nested\":{\"x\":1,\"y\":2}}"
list = "[3,1,2]"
scalar = "42"
```

**Notes**

- Object keys are sorted at every level, so the output is independent of source key order.
- Pair with %canonicalHash to fingerprint a value.
