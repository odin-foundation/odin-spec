# %last

last element of an array

**Signature:** `%last <array> -> any`

Return the last element of an array reference, preserving its type.

**Transform**

```odin
{out}
l = %last @.names
```

**In**

```odin
{names[] : ~}
"alice"
"bob"
```

**Out**

```odin
{out}
l = "bob"
```

**Notes**

- Type-preserving: a string element comes back a string, a number stays a number.
- An empty or missing array yields ~ (null).

**Avoid**

- `l = %last @.x (where x is a scalar)`: %last indexes into an array; a scalar reference resolves to no array and returns ~ instead of the value
