# %upper

uppercase a string

**Signature:** `%upper <string> -> string`

Uppercase every character of a string value.

**Transform**

```odin
{out}
upper = %upper @.name
alreadyUpper = %upper @.code
mixed = %upper @.label
```

**In**

```odin
name = "honda"
code = "ABC"
label = "Honda-2024!"
```

**Out**

```odin
{out}
upper = "HONDA"
alreadyUpper = "ABC"
mixed = "HONDA-2024!"
```

**Notes**

- First and only argument is the string to uppercase.
- The argument must be a reference (@.name) or a quoted literal - a bare word is read as a literal string, not a path.

**Avoid**

- `upper = %upper`: missing the source argument - the engine raises a verb-arity error
- `upper = %upper name`: bare word name is read as the literal string "name", producing "NAME"; use @.name to read the field
