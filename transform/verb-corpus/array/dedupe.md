# %dedupe

remove duplicate records by key

**Signature:** `%dedupe <array> <string:keyField> -> array`

Drop later records that repeat a key-field value, keeping the first.

**Transform**

```odin
{out}
uniq = %dedupe @.records "id"
noMatch = %dedupe @.distinctRecs "id"
nonArray = %dedupe @.notArr "id"
```

**In**

```odin
notArr = "x"
records = "[{\"id\": \"r1\", \"name\": \"first\"}, {\"id\": \"r1\", \"name\": \"dup\"}, {\"id\": \"r2\", \"name\": \"second\"}]"
distinctRecs = "[{\"id\": \"a\", \"name\": \"one\"}, {\"id\": \"b\", \"name\": \"two\"}]"
```

**Out**

```odin
{out}
{.uniq[] : id, name}
"r1", "first"
"r2", "second"
{.noMatch[] : id, name}
"a", "one"
"b", "two"
nonArray = ~
```

**Notes**

- Unlike %distinct, it compares one key field, not the whole value.
- The record array is supplied as a JSON-array string so the key field reads as a plain value.

**Avoid**

- `{records[] : id, name} "r1", "first" "r1", "dup"`: a typed ODIN object-array block wraps each field, so the key field is not found and no rows are deduplicated; supply the records as a JSON-array string literal instead
