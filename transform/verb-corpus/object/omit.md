# %omit

drop named keys

**Signature:** `%omit <object> <key...> -> object`

Return a copy of an object with the named keys removed.

**Transform**

```odin
{out}
o = %omit @.rec "active"
```

**In**

```odin
{rec}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.o}
name = "Ada"
role = "admin"
```

**Notes**

- Keys not present in the object are ignored; remaining key order is preserved.
