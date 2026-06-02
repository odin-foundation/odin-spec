# %trimRight

strip trailing whitespace

**Signature:** `%trimRight <string> -> string`

Remove whitespace from the end of a string only.

**Transform**

```odin
{out}
out = %trimRight @.raw
```

**In**

```odin
raw = "  hello  "
```

**Out**

```odin
{out}
out = "  hello"
```

**Notes**

- Only trailing whitespace is removed; leading whitespace is preserved.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trimRight`: missing the source argument - the engine raises a verb-arity error
