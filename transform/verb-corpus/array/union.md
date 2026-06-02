# %union

distinct elements from both arrays

**Signature:** `%union <array> <array> -> array`

Return the distinct elements from both arrays.

**Transform**

```odin
{out}
all = %union @.a @.b
```

**In**

```odin
{a[] : ~}
##1
##2
##2

{b[] : ~}
##2
##3
```

**Out**

```odin
{out}
{.all[] : ~}
##1
##2
##3
```

**Notes**

- Elements are de-duplicated; first-array order precedes new elements from the second.
