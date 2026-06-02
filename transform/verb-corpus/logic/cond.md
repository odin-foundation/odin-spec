# %cond

multi-branch conditional

**Signature:** `%cond <cond1:boolean> <val1> [<cond2:boolean> <val2> ...] <default> -> value`

Evaluate condition/value pairs and return the value of the first true condition, else a default.

**Transform**

```odin
{out}
code = %cond %eq @.status "active" "A" %eq @.status "pending" "P" "X"
```

**In**

```odin
status = "pending"
```

**Out**

```odin
{out}
code = "P"
```

**Notes**

- Arguments are read as condition/value pairs left-to-right; the first truthy condition wins.
- A trailing odd argument is the default returned when no condition matches.

**Avoid**

- `code = %cond %eq @.status "active" "A"`: with an even argument count there is no trailing default, so when the single condition is false %cond returns null (~) instead of a fallback. Always append a default value.
