# %renameKeys

rename keys via a mapping

**Signature:** `%renameKeys <object> <mapping> -> object`

Rename object keys according to an old-to-new mapping object.

**Transform**

```odin
{out}
renamed = %renameKeys @.rec @.mapping
```

**In**

```odin
{rec}
fn = "Ada"
keep = "as-is"

{mapping}
fn = "firstName"
```

**Out**

```odin
{out}
{.renamed}
firstName = "Ada"
keep = "as-is"
```

**Notes**

- Keys absent from the mapping are passed through unchanged.
