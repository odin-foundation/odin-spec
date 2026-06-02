# %escapeHtml

escape HTML special characters

**Signature:** `%escapeHtml <string> -> string`

Escape the five HTML special characters & < > " and the apostrophe as entities.

**Transform**

```odin
{out}
escaped = %escapeHtml @.raw
empty = %escapeHtml @.blank
```

**In**

```odin
raw = "<p>1 & 2</p>"
blank = ""
```

**Out**

```odin
{out}
escaped = "&lt;p&gt;1 &amp; 2&lt;/p&gt;"
empty = ""
```

**Notes**

- The apostrophe is escaped as &#39;. Invert with %unescapeHtml.
