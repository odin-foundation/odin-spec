# %lower

lowercase a string

**Signature:** `%lower <string> -> string`

Lowercase every character of a string value.

**Transform**

```odin
{out}
name = %lower @.name
mixed = %lower @.mixed
empty = %lower @.empty
```

**In**

```odin
name = "HONDA"
mixed = "Honda CIVIC"
empty = ""
```

**Out**

```odin
{out}
name = "honda"
mixed = "honda civic"
empty = ""
```

**Notes**

- First and only argument is the string to lowercase.
- The argument must be a reference (@.name) or a quoted literal - a bare word is read as a literal string, not a path.

**Avoid**

- `out = %lower`: missing the source argument - the engine raises a verb-arity error
- `out = %lower name`: bare word name is read as the literal string "name", producing "name"; use @.name to read the field
