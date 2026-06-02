# %canonicalHash

sha256 of the canonical form

**Signature:** `%canonicalHash <any> -> string`

Hash a value by its canonical (sorted-key) JSON form, yielding an order-independent fingerprint.

**Transform**

```odin
{out}
h1 = %canonicalHash @.a
h2 = %canonicalHash @.b
```

**In**

```odin
{a}
b = ##2
a = ##1

{b}
a = ##1
b = ##2
```

**Out**

```odin
{out}
h1 = "43258cff783fe7036d8a43033f830adfc60ec037382473548ac742b888292777"
h2 = "43258cff783fe7036d8a43033f830adfc60ec037382473548ac742b888292777"
```

**Notes**

- The digest is the lowercase hex sha256 of the %stableStringify output, so reordering keys does not change it.
