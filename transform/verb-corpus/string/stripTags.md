# %stripTags

remove HTML/XML tags

**Signature:** `%stripTags <string> -> string`

Remove angle-bracket tags from a string, leaving the text content.

**Transform**

```odin
{out}
out = %stripTags @.raw
```

**In**

```odin
raw = "<p>Hello <b>world</b></p>"
```

**Out**

```odin
{out}
out = "Hello world"
```

**Notes**

- Removes anything matching <...>; it does not decode entities in the remaining text.
