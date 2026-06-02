# %capitalize

capitalize the first letter

**Signature:** `%capitalize <string> -> string`

Uppercase the first character and lowercase the rest.

**Transform**

```odin
{out}
out = %capitalize @.word
```

**In**

```odin
word = "hONDA"
```

**Out**

```odin
{out}
out = "Honda"
```

**Notes**

- First character is uppercased and every following character is lowercased.
- Operates on the whole string as one word - use %titleCase for per-word capitalization.

**Avoid**

- `out = %capitalize honda`: bare word honda is read as the literal string, capitalizing the literal instead of the field; use @.word
