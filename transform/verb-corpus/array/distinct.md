# %distinct

remove duplicate values

**Signature:** `%distinct <array> [<string:field>] -> array`

Drop repeated values from a primitive array, keeping first occurrence.

**Transform**

```odin
{out}
uniq = %distinct @.tags
allUnique = %distinct @.codes
```

**In**

```odin
{tags[] : ~}
"auto"
"home"
"auto"

{codes[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
{.uniq[] : ~}
"auto"
"home"
{.allUnique[] : ~}
"a"
"b"
"c"
```

**Notes**

- With no field argument it deduplicates primitive values.
- A second quoted field argument deduplicates an object array by that field.
