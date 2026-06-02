# %titleCase

capitalize each word

**Signature:** `%titleCase <string> -> string`

Lowercase the string, then uppercase the first letter of every whitespace-separated word.

**Transform**

```odin
{out}
out = %titleCase @.phrase
```

**In**

```odin
phrase = "honda civic type r"
```

**Out**

```odin
{out}
out = "Honda Civic Type R"
```

**Notes**

- Words are split on whitespace; each word's first character is uppercased and the remainder lowercased.
- Use %capitalize to title-case only the very first letter of the whole string.
