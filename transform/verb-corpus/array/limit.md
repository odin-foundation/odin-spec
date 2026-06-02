# %limit

first N items

**Signature:** `%limit <array> <integer:count> -> array`

Return the first count items (alias of %take).

**Transform**

```odin
{out}
top2 = %limit @.results ##2
```

**In**

```odin
{results[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
{.top2[] : ~}
"a"
"b"
```

**Notes**

- Alias for %take; useful for pagination or capping result sets.
