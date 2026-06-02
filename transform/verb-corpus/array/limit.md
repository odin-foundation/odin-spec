# %limit

first N items

**Signature:** `%limit <array> <integer:count> -> array`

Return the first count items (alias of %take).

**Transform**

```odin
{out}
top2 = %limit @.results ##2
over = %limit @.results ##10
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
{.over[] : ~}
"a"
"b"
"c"
```

**Notes**

- Alias for %take; useful for pagination or capping result sets.
