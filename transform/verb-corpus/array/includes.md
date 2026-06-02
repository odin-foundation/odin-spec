# %includes

membership test

**Signature:** `%includes <array> <value> -> boolean`

Return true when an array contains a value.

**Transform**

```odin
{out}
hasCA = %includes @.states "CA"
absent = %includes @.states "FL"
```

**In**

```odin
{states[] : ~}
"NY"
"CA"
"TX"
```

**Out**

```odin
{out}
hasCA = ?true
absent = ?false
```

**Notes**

- Compares by string value, so it works on typed primitive arrays.
