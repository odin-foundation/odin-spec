# %concat

join strings end to end

**Signature:** `%concat <string> <string> [<string>...] -> string`

Concatenate two or more values into a single string.

**Transform**

```odin
{out}
fullName = %concat @.firstName " " @.lastName
```

**In**

```odin
firstName = "John"
lastName = "Smith"
```

**Out**

```odin
{out}
fullName = "John Smith"
```

**Notes**

- Arguments are concatenated verbatim with no separator inserted.
- Provide any separator as its own literal argument (here the quoted space " ").

**Avoid**

- `fullName = %concat @.firstName @.lastName`: no separator argument yields "JohnSmith"; pass " " between the fields
