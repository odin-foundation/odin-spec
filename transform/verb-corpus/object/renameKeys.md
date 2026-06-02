# %renameKeys

rename keys via a mapping

**Signature:** `%renameKeys <object> <mapping> -> object`

Rename object keys according to an old-to-new mapping object.

**Transform**

```odin
{out}
r = %renameKeys @.rec @.mapping
```

**In**

```odin
{rec}
fn = "Ada"
ln = "Lovelace"

{mapping}
fn = "firstName"
ln = "lastName"
```

**Out**

```odin
{out}
{.r}
firstName = "Ada"
lastName = "Lovelace"
```

**Notes**

- Keys absent from the mapping are passed through unchanged.
