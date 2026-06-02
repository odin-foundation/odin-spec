# %titleCase

capitalize each word

**Signature:** `%titleCase <string> -> string`

Lowercase the string, then uppercase the first letter of every whitespace-separated word.

**Transform**

```odin
{out}
out = %titleCase @.phrase
mixed = %titleCase @.shouty
single = %titleCase @.word
```

**In**

```odin
phrase = "honda civic type r"
shouty = "HELLO WORLD"
word = "honda"
```

**Out**

```odin
{out}
out = "Honda Civic Type R"
mixed = "Hello World"
single = "Honda"
```

**Notes**

- Words are split on whitespace; each word's first character is uppercased and the remainder lowercased.
- Use %capitalize to title-case only the very first letter of the whole string.
