# %trim

strip leading and trailing whitespace

**Signature:** `%trim <string> -> string`

Remove whitespace from both ends of a string.

**Transform**

```odin
{out}
out = %trim @.raw
```

**In**

```odin
raw = "  hello  "
```

**Out**

```odin
{out}
out = "hello"
```

**Notes**

- Trims both ends; interior whitespace is left untouched.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trim`: missing the source argument - the engine raises a verb-arity error
