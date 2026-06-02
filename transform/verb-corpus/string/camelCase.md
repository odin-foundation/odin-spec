# %camelCase

convert to camelCase

**Signature:** `%camelCase <string> -> string`

Re-case a delimited or mixed-case string into camelCase.

**Transform**

```odin
{out}
out = %camelCase @.name
snake = %camelCase @.snake
spaced = %camelCase @.spaced
allCaps = %camelCase @.allCaps
```

**In**

```odin
name = "hello-world"
snake = "first_name_field"
spaced = "Two Words"
allCaps = "HTTP"
```

**Out**

```odin
{out}
out = "helloWorld"
snake = "firstNameField"
spaced = "twoWords"
allCaps = "http"
```

**Notes**

- Splits on hyphens, underscores, spaces, and case boundaries, then joins with the first word lowercased and the rest capitalized.
