# %defaults

fill missing keys

**Signature:** `%defaults <object> <defaults> -> object`

Merge a defaults object under a base object, filling only keys the base lacks.

**Transform**

```odin
{out}
merged = %defaults @.rec @.fallback
```

**In**

```odin
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
```

**Notes**

- Existing keys in the base object are kept; only absent keys are taken from the defaults.
