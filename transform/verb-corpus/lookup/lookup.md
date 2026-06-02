# %lookup

map a key through a table

**Signature:** `%lookup <string:"TABLE.column"> <key> [<key>...] -> value`

Translate a value via a declared lookup table.

**Transform**

```odin
{$table.BODY_TYPES[name, code]}
"sedan", "SD"
"coupe", "CP"
"suv", "SU"

{out}
vehicleCode = %lookup "BODY_TYPES.code" @.vehicleType
suvCode = %lookup "BODY_TYPES.code" @.suvType
unknownCode = %lookup "BODY_TYPES.code" @.unknownType
```

**In**

```odin
vehicleType = "sedan"
suvType = "suv"
unknownType = "wagon"
```

**Out**

```odin
{out}
vehicleCode = "SD"
suvCode = "SU"
unknownCode = ~
```

**Notes**

- The table is declared with a {$table.NAME[col1, col2]} block above the output section.
- The first argument is the quoted "TABLE.column" naming the column to return; remaining arguments are the keys to match.
- A key with no matching row resolves to ~ (null).

**Avoid**

- `vehicleCode = %lookup @.vehicleType "BODY_TYPES.code"`: argument order is "TABLE.column" first then the key; reversing them matches nothing and yields ~
- `vehicleCode = %lookup "code" @.vehicleType`: the column reference must include the table name ("BODY_TYPES.code"); a bare column resolves to ~
