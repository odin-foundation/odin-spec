# %replace

replace all literal occurrences

**Signature:** `%replace <string> <find:string> <replacement:string> -> string`

Replace every literal occurrence of a substring with a replacement.

**Transform**

```odin
{out}
hit = %replace @.name "o" "0"
miss = %replace @.name "z" "0"
all = %replace @.many "a" "A"
```

**In**

```odin
name = "Honda"
many = "banana"
```

**Out**

```odin
{out}
hit = "H0nda"
miss = "Honda"
all = "bAnAnA"
```

**Notes**

- Three required arguments: string, literal find, literal replacement.
- Matching is literal (not regex) and replaces all occurrences. Use %replaceRegex for pattern matching.

**Avoid**

- `out = %replace @.name "o"`: missing the replacement argument - the engine raises a 3-argument arity error
