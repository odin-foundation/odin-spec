# %concat

join strings end to end

**Signature:** `%concat <string> <string> [<string>...] -> string`

Concatenate two or more values into a single string.

**Transform**

```odin
{out}
fullName = %concat @.firstName " " @.lastName
three = %concat @.firstName "-" @.lastName
numeric = %concat "id" @.num
```

**In**

```odin
firstName = "John"
lastName = "Smith"
num = ##42
```

**Out**

```odin
{out}
fullName = "John Smith"
three = "John-Smith"
numeric = "id42"
```

**Notes**

- Arguments are concatenated verbatim with no separator inserted.
- Provide any separator as its own literal argument (here the quoted space " ").

**Avoid**

- `fullName = %concat @.firstName @.lastName`: no separator argument yields "JohnSmith"; pass " " between the fields
