# %rowNumber

add sequential row numbers

**Signature:** `%rowNumber <array> -> array`

Tag each element with a 1-based _rowNum field.

**Transform**

```odin
{out}
numbered = %rowNumber @.names
records = %rowNumber @.people
```

**In**

```odin
names = "[\"alice\", \"bob\", \"carol\"]"
people = "[{\"name\": \"ann\"}, {\"name\": \"bo\"}]"
```

**Out**

```odin
{out}
{.numbered[] : _rowNum, value}
##1, "alice"
##2, "bob"
##3, "carol"
{.records[] : _rowNum, name}
##1, "ann"
##2, "bo"
```

**Notes**

- Primitive elements are wrapped as { _rowNum, value } objects.
- The array is supplied as a JSON-array string so the added _rowNum field surfaces in the output.

**Avoid**

- `{names[] : ~} "alice" "bob"`: a typed ODIN array block wraps each element, so the added _rowNum field is dropped from the serialized output; supply the array as a JSON-array string literal instead
