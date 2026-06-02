# %symmetricDifference

elements in exactly one array

**Signature:** `%symmetricDifference <array> <array> -> array`

Return the distinct elements that appear in exactly one of the two arrays.

**Transform**

```odin
{out}
xor = %symmetricDifference @.a @.b
disjoint = %symmetricDifference @.c @.d
deduped = %symmetricDifference @.e @.f
```

**In**

```odin
{a[] : ~}
##1
##2
##3

{b[] : ~}
##2
##3
##4

{c[] : ~}
##1
##2

{d[] : ~}
##3
##4

{e[] : ~}
##1
##1
##2

{f[] : ~}
##2
##3
```

**Out**

```odin
{out}
{.xor[] : ~}
##1
##4
{.disjoint[] : ~}
##1
##2
##3
##4
{.deduped[] : ~}
##1
##3
```

**Notes**

- First-array exclusives precede second-array exclusives; the result is distinct.
- Disjoint arrays return every element; duplicates within an input are collapsed.
