# %first

first element of an array

**Signature:** `%first <array> -> any`

Return the first element of an array reference, preserving its type.

**Transform**

```odin
{out}
f = %first @.names
numeric = %first @.amounts
single = %first @.one
```

**In**

```odin
{names[] : ~}
"alice"
"bob"

{amounts[] : ~}
##10
##20
##30

{one[] : ~}
##42
```

**Out**

```odin
{out}
f = "alice"
numeric = ##10
single = ##42
```

**Notes**

- Type-preserving: a string element comes back a string, a number stays a number.
- An empty or missing array yields ~ (null).

**Avoid**

- `f = %first @.x (where x is a scalar)`: %first indexes into an array; a scalar reference resolves to no array and returns ~ instead of the value
