# %defaults

fill missing keys

**Signature:** `%defaults <object> <defaults> -> object`

Merge a defaults object under a base object, filling only keys the base lacks.

**Transform**

```odin
{out}
merged = %defaults @.rec @.fallback
nonObject = %defaults @.notObj @.fallback
```

**In**

```odin
notObj = "x"

{rec}
name = "Ada"

{fallback}
name = "Anon"
role = "guest"
```

**Out**

```odin
{out}
{.merged}
name = "Ada"
role = "guest"
{.nonObject}
name = "Anon"
role = "guest"
```

**Notes**

- Existing keys in the base object are kept; only absent keys are taken from the defaults.
