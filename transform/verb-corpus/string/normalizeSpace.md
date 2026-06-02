# %normalizeSpace

collapse and trim whitespace

**Signature:** `%normalizeSpace <string> -> string`

Collapse runs of whitespace to single spaces and trim the ends.

**Transform**

```odin
{out}
collapsed = %normalizeSpace @.raw
tabs = %normalizeSpace @.tabs
clean = %normalizeSpace @.clean
```

**In**

```odin
raw = "  a   b  "
tabs = "a\tb\nc"
clean = "already clean"
```

**Out**

```odin
{out}
collapsed = "a b"
tabs = "a b c"
clean = "already clean"
```

**Notes**

- Single argument. Interior whitespace runs become one space and leading/trailing whitespace is removed.
