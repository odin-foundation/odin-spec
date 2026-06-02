# %escapeHtml

escape HTML special characters

**Signature:** `%escapeHtml <string> -> string`

Escape the five HTML special characters & < > " and the apostrophe as entities.

**Transform**

```odin
{out}
out = %escapeHtml @.raw
```

**In**

```odin
raw = "<p>1 & 2</p>"
```

**Out**

```odin
{out}
out = "&lt;p&gt;1 &amp; 2&lt;/p&gt;"
```

**Notes**

- The apostrophe is escaped as &#39;. Invert with %unescapeHtml.
