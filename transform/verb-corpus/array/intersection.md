# %intersection

elements in both arrays

**Signature:** `%intersection <array> <array> -> array`

Return the distinct elements present in both arrays.

**Transform**

```odin
{out}
both = %intersection @.a @.b
withOne = %intersection @.a @.c
```

**In**

```odin
{a[] : ~}
##1
##2
##2
##3

{b[] : ~}
##2
##3
##4

{c[] : ~}
##3
##5
```

**Out**

```odin
{out}
{.both[] : ~}
##2
##3
{.withOne[] : ~}
##3
```

**Notes**

- Result elements are distinct; order follows the first array.
