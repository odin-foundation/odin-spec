# %matches

regex test (alias of %match)

**Signature:** `%matches <string> <pattern:string> -> boolean`

Return true when a string matches a regular expression anywhere.

**Transform**

```odin
{out}
out = %matches @.email "^[\\w.+-]+@[\\w-]+\\.[a-z]{2,}$"
```

**In**

```odin
email = "a@b.com"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Behaves like %match. Two required arguments: the string and the regex pattern.
- Backslashes in the pattern are escaped within the ODIN string literal (\\w for \w). Returns a boolean (? prefix).
