# %unescapeHtml

decode HTML entities

**Signature:** `%unescapeHtml <string> -> string`

Decode named and numeric HTML entities back to their characters.

**Transform**

```odin
{out}
decoded = %unescapeHtml @.raw
numeric = %unescapeHtml @.refs
```

**In**

```odin
raw = "&lt;p&gt;1 &amp; 2&lt;/p&gt;"
refs = "&#65;&#x42;"
```

**Out**

```odin
{out}
decoded = "<p>1 & 2</p>"
numeric = "AB"
```

**Notes**

- Decodes &amp; &lt; &gt; &quot; &#39; &apos; plus numeric (&#65;) and hex (&#x41;) references.
