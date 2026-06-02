# %clean

strip control chars and normalize whitespace

**Signature:** `%clean <string> -> string`

Remove control characters, normalize Unicode whitespace to spaces, collapse runs, and trim.

**Transform**

```odin
{out}
out = %clean @.raw
tabs = %clean @.tabs
clean = %clean @.tidy
```

**In**

```odin
raw = "  hello   world  "
tabs = "a\t\tb\nc"
tidy = "already clean"
```

**Out**

```odin
{out}
out = "hello world"
tabs = "a b c"
clean = "already clean"
```

**Notes**

- Single argument. Removes ASCII control characters, maps assorted Unicode spaces to a regular space, then collapses whitespace and trims.
- Use %normalizeSpace when only whitespace collapsing is needed.
