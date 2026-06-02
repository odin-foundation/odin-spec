# %trimLeft

strip leading whitespace

**Signature:** `%trimLeft <string> -> string`

Remove whitespace from the start of a string only.

**Transform**

```odin
{out}
out = %trimLeft @.raw
leadingOnly = %trimLeft @.front
noLead = %trimLeft @.clean
```

**In**

```odin
raw = "  hello  "
front = "   abc"
clean = "abc  "
```

**Out**

```odin
{out}
out = "hello  "
leadingOnly = "abc"
noLead = "abc  "
```

**Notes**

- Only leading whitespace is removed; trailing whitespace is preserved.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trimLeft`: missing the source argument - the engine raises a verb-arity error
