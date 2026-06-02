# %contains

substring test

**Signature:** `%contains <string> <search:string> -> boolean`

Return true when the string contains the given substring.

**Transform**

```odin
{out}
out = %contains @.name "ond"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two arguments: the haystack string then the quoted needle.
- Returns a boolean (? prefix in ODIN output). The match is case-sensitive.

**Avoid**

- `out = %contains @.name ond`: bare word ond is not a quoted literal or path, so it is dropped and the engine raises a 2-argument arity error; quote the needle as "ond"
