# %clean

strip control chars and normalize whitespace

**Signature:** `%clean <string> -> string`

Remove control characters, normalize Unicode whitespace to spaces, collapse runs, and trim.

**Transform**

```odin
{out}
out = %clean @.raw
```

**In**

```odin
raw = "  hello   world  "
```

**Out**

```odin
{out}
out = "hello world"
```

**Notes**

- Single argument. Removes ASCII control characters, maps assorted Unicode spaces to a regular space, then collapses whitespace and trims.
- Use %normalizeSpace when only whitespace collapsing is needed.
