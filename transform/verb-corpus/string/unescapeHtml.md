# %unescapeHtml

decode HTML entities

**Signature:** `%unescapeHtml <string> -> string`

Decode named and numeric HTML entities back to their characters.

**Transform**

```odin
{out}
out = %unescapeHtml @.raw
```

**In**

```odin
raw = "&lt;p&gt;1 &amp; 2&lt;/p&gt;"
```

**Out**

```odin
{out}
out = "<p>1 & 2</p>"
```

**Notes**

- Decodes &amp; &lt; &gt; &quot; &#39; &apos; plus numeric (&#65;) and hex (&#x41;) references.
