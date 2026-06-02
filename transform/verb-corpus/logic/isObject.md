# %isObject

object-type test

**Signature:** `%isObject <value> -> boolean`

Return true when a value is a nested object.

**Transform**

```odin
{out}
isObj = %isObject @.address
```

**In**

```odin
{address}
city = "Reno"
```

**Out**

```odin
{out}
isObj = ?true
```

**Notes**

- The source nests fields under an {address} section, producing an object value.
- Complements %isArray for telling structured values apart.
