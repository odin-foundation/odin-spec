# %extract

pull a regex capture group

**Signature:** `%extract <string> <pattern:string> [group:integer] -> string`

Return a capture group from the first regex match in a string.

**Transform**

```odin
{out}
out = %extract @.text "([0-9]+)" ##1
whole = %extract @.text "[0-9]+" ##0
noMatch = %extract @.text "([a-z]{20})" ##1
badGroup = %extract @.text "([0-9]+)" ##5
```

**In**

```odin
text = "order 42 placed"
```

**Out**

```odin
{out}
out = "42"
whole = "42"
noMatch = ~
badGroup = ~
```

**Notes**

- Two required arguments plus an optional group index (default 0 = whole match, 1 = first capture group).
- Returns null when the pattern does not match or the group index is out of range.

**Avoid**

- `out = %extract "text" "([0-9]+)" ##1`: quoting the first argument as "text" matches against the literal word, which has no digits, so the result is null (~); use @.text to read the field
