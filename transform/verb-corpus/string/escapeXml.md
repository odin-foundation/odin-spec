# %escapeXml

escape XML special characters

**Signature:** `%escapeXml <string> -> string`

Escape & < > " and the apostrophe as XML entities, using &apos; for the apostrophe.

**Transform**

```odin
{out}
escaped = %escapeXml @.raw
```

**In**

```odin
raw = "x = 'a' & b"
```

**Out**

```odin
{out}
escaped = "x = &apos;a&apos; &amp; b"
```

**Notes**

- Differs from %escapeHtml only in the apostrophe, which becomes &apos; (valid XML).
