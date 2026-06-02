# %template

fill {placeholders} from an object

**Signature:** `%template <template> <object> -> string`

Substitute {key} placeholders in a template string with values from an object.

**Transform**

```odin
{out}
msg = %template @.tpl @.data
```

**In**

```odin
tpl = "Hi {name}, you are {age}"
{data}
name = "Ada"
age = ##36
```

**Out**

```odin
{out}
msg = "Hi Ada, you are 36"
```

**Notes**

- Placeholders name fields of the object; a missing key renders as an empty string.
- Whitespace inside braces is trimmed, so {name} and { name } are equivalent.
