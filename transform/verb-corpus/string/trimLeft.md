# %trimLeft

strip leading whitespace

**Signature:** `%trimLeft <string> -> string`

Remove whitespace from the start of a string only.

**Transform**

```odin
{out}
out = %trimLeft @.raw
```

**In**

```odin
raw = "  hello  "
```

**Out**

```odin
{out}
out = "hello  "
```

**Notes**

- Only leading whitespace is removed; trailing whitespace is preserved.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trimLeft`: missing the source argument - the engine raises a verb-arity error
