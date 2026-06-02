# %formatPercent

fraction to percentage string

**Signature:** `%formatPercent <number> <integer:decimals> -> string`

Format a fractional ratio as a percentage string with a fixed number of decimals.

**Transform**

```odin
{out}
v = %formatPercent @.a ##1
```

**In**

```odin
a = #0.1234
```

**Out**

```odin
{out}
v = "12.3%"
```

**Notes**

- Multiplies by 100 and appends a percent sign (0.1234 → "12.3%").
- The result is a string.

**Avoid**

- `v = %formatPercent @.a`: the engine requires the decimals argument; omitting it raises a verb-arity error even though it reads like an optional parameter
