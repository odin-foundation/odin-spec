# %stripTags

remove HTML/XML tags

**Signature:** `%stripTags <string> -> string`

Remove angle-bracket tags from a string, leaving the text content.

**Transform**

```odin
{out}
stripped = %stripTags @.raw
noTags = %stripTags @.plain
```

**In**

```odin
raw = "<p>Hello <b>world</b></p>"
plain = "no tags here"
```

**Out**

```odin
{out}
stripped = "Hello world"
noTags = "no tags here"
```

**Notes**

- Removes anything matching <...>; it does not decode entities in the remaining text.
