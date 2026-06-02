# %replace

replace all literal occurrences

**Signature:** `%replace <string> <find:string> <replacement:string> -> string`

Replace every literal occurrence of a substring with a replacement.

**Transform**

```odin
{out}
out = %replace @.name "o" "0"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "H0nda"
```

**Notes**

- Three required arguments: string, literal find, literal replacement.
- Matching is literal (not regex) and replaces all occurrences. Use %replaceRegex for pattern matching.

**Avoid**

- `out = %replace @.name "o"`: missing the replacement argument - the engine raises a 3-argument arity error
