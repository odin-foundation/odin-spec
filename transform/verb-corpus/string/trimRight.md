# %trimRight

strip trailing whitespace

**Signature:** `%trimRight <string> -> string`

Remove whitespace from the end of a string only.

**Transform**

```odin
{out}
out = %trimRight @.raw
trailingOnly = %trimRight @.back
noTrail = %trimRight @.clean
```

**In**

```odin
raw = "  hello  "
back = "abc   "
clean = "  abc"
```

**Out**

```odin
{out}
out = "  hello"
trailingOnly = "abc"
noTrail = "  abc"
```

**Notes**

- Only trailing whitespace is removed; leading whitespace is preserved.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trimRight`: missing the source argument - the engine raises a verb-arity error
