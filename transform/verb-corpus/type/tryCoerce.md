# %tryCoerce

auto-detect and coerce a string to its natural type

**Signature:** `%tryCoerce <value> -> value`

Infer the type of a string value (integer, number, boolean, date) and coerce it, leaving plain text unchanged.

**Transform**

```odin
{out}
v = %tryCoerce @.raw
float = %tryCoerce @.float
flag = %tryCoerce @.flag
word = %tryCoerce @.word
```

**In**

```odin
raw = "42"
float = "3.14"
flag = "true"
word = "John"
```

**Out**

```odin
{out}
v = ##42
float = #3.14
flag = ?true
word = "John"
```

**Notes**

- Detection order is null/empty, boolean, integer, number, ISO date, ISO timestamp; the first match wins.
- "3.14" detects as #3.14, "true" as ?true, and a plain word like "John" passes through as the string "John".
- Designed for XML/CSV ingests where every field arrives as a string; non-string inputs are returned unchanged.

**Avoid**

- `v = %tryCoerce @.raw ; input "John" expecting a number`: values that match no type pattern are returned as the original string, not coerced or nulled
