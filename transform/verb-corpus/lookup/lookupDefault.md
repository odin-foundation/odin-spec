# %lookupDefault

map a key through a table with a fallback

**Signature:** `%lookupDefault <string:"TABLE.column"> <key> [<key>...] <default> -> value`

Translate a value via a declared lookup table, returning a default when no row matches.

**Transform**

```odin
{$table.BODY_TYPES[name, code]}
"sedan", "SD"
"coupe", "CP"
"suv", "SU"

{out}
vehicleCode = %lookupDefault "BODY_TYPES.code" @.vehicleType "??"
```

**In**

```odin
vehicleType = "wagon"
```

**Out**

```odin
{out}
vehicleCode = "??"
```

**Notes**

- The table is declared with a {$table.NAME[col1, col2]} block above the output section.
- The first argument is the quoted "TABLE.column" naming the column to return; the keys to match follow.
- The last argument is the default value, returned when no row matches (here "wagon" is absent, so "??").
- A matching key returns the cell value instead of the default.

**Avoid**

- `vehicleCode = %lookupDefault "BODY_TYPES.code" @.vehicleType`: the default argument is required; with only the table reference and one key the call has too few arguments and resolves to ~
- `vehicleCode = %lookupDefault @.vehicleType "BODY_TYPES.code" "??"`: the "TABLE.column" reference must come first; reversing it with the key matches nothing and returns the default
