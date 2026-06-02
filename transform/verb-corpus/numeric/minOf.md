# %minOf

minimum of scalar arguments

**Signature:** `%minOf <number> <number> ... -> number`

Return the smallest of two or more scalar numeric arguments.

**Transform**

```odin
{out}
v = %minOf @.a @.b @.c
negatives = %minOf @.n1 @.n2 @.n3
pair = %minOf @.p1 @.p2
```

**In**

```odin
a = #9
b = #4
c = #7
n1 = #-2
n2 = #-9
n3 = #5
p1 = #3
p2 = #8
```

**Out**

```odin
{out}
v = ##4
negatives = ##-9
pair = ##3
```

**Notes**

- Variadic: pass each operand as a separate scalar argument.
- This is not an array-reducing verb - to take the minimum of an array, fan its elements out as individual references.

**Avoid**

- `v = %minOf @.list`: %minOf compares its scalar arguments; passing a single array reference coerces the array to its length, not its minimum element
