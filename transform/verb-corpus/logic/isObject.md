# %isObject

object-type test

**Signature:** `%isObject <value> -> boolean`

Return true when a value is a nested object.

**Transform**

```odin
{out}
plain = %isObject @.name
nullVal = %isObject @.missing
isObj = %isObject @.address
```

**In**

```odin
name = "Ada"
missing = ~

{address}
city = "Reno"
```

**Out**

```odin
{out}
plain = ?false
nullVal = ?false
isObj = ?true
```

**Notes**

- The source nests fields under an {address} section, producing an object value.
- Complements %isArray for telling structured values apart.
