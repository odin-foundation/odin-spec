# %union

distinct elements from both arrays

**Signature:** `%union <array> <array> -> array`

Return the distinct elements from both arrays.

**Transform**

```odin
{out}
all = %union @.a @.b
disjoint = %union @.c @.d
fromB = %union @.empty @.b
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

{c[] : ~}
##1
##2

{d[] : ~}
##3
##4

{empty[] : ~}
```

**Out**

```odin
{out}
{.all[] : ~}
##1
##2
##3
{.disjoint[] : ~}
##1
##2
##3
##4
{.fromB[] : ~}
##2
##3
```

**Notes**

- Elements are de-duplicated; first-array order precedes new elements from the second.
- An empty first array yields the distinct elements of the second.
