# %ifEmpty

fallback when empty string

**Signature:** `%ifEmpty <value> <fallback> -> value`

Return a fallback value when the input is an empty string, otherwise the input.

**Transform**

```odin
{out}
label = %ifEmpty @.label "untitled"
kept = %ifEmpty @.title "untitled"
whitespaceKept = %ifEmpty @.spaces "untitled"
```

**In**

```odin
label = ""
title = "Report"
spaces = "  "
```

**Out**

```odin
{out}
label = "untitled"
kept = "Report"
whitespaceKept = "  "
```

**Notes**

- Triggers only on the empty string ""; a null (~) is not empty here - use %ifNull for null.
- Whitespace-only strings are not empty; trim first with %trim if you want that behavior.
