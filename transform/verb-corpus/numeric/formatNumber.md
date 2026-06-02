# %formatNumber

fixed decimal-place string

**Signature:** `%formatNumber <number> <integer:decimals> -> string`

Format a number as a string with a fixed number of decimal places.

**Transform**

```odin
{out}
v = %formatNumber @.a ##3
```

**In**

```odin
a = #3.14159
```

**Out**

```odin
{out}
v = "3.142"
```

**Notes**

- The result is a string (quoted), not a number - trailing/rounded decimals are fixed by toFixed.
- The decimals argument is required.

**Avoid**

- `v = %formatNumber @.a`: the decimals argument is required; omitting it raises a verb-arity error
