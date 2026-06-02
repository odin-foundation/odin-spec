# %maxOf

maximum of scalar arguments

**Signature:** `%maxOf <number> <number> ... -> number`

Return the largest of two or more scalar numeric arguments.

**Transform**

```odin
{out}
v = %maxOf @.a @.b @.c
```

**In**

```odin
a = #9
b = #4
c = #7
```

**Out**

```odin
{out}
v = ##9
```

**Notes**

- Variadic: pass each operand as a separate scalar argument.
- This is not an array-reducing verb - to take the maximum of an array, fan its elements out as individual references.

**Avoid**

- `v = %maxOf @.list`: %maxOf compares its scalar arguments; passing a single array reference coerces the array to its length, not its maximum element
