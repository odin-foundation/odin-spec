# %escapeXml

escape XML special characters

**Signature:** `%escapeXml <string> -> string`

Escape & < > " and the apostrophe as XML entities, using &apos; for the apostrophe.

**Transform**

```odin
{out}
escaped = %escapeXml @.raw
angles = %escapeXml @.tag
plain = %escapeXml @.plain
```

**In**

```odin
raw = "x = 'a' & b"
tag = "<a href=\"u\">"
plain = "no specials"
```

**Out**

```odin
{out}
escaped = "x = &apos;a&apos; &amp; b"
angles = "&lt;a href=&quot;u&quot;&gt;"
plain = "no specials"
```

**Notes**

- Differs from %escapeHtml only in the apostrophe, which becomes &apos; (valid XML).
