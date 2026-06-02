# %normalizeSpace

collapse and trim whitespace

**Signature:** `%normalizeSpace <string> -> string`

Collapse runs of whitespace to single spaces and trim the ends.

**Transform**

```odin
{out}
out = %normalizeSpace @.raw
```

**In**

```odin
raw = "  a   b  "
```

**Out**

```odin
{out}
out = "a b"
```

**Notes**

- Single argument. Interior whitespace runs become one space and leading/trailing whitespace is removed.
