# %matches

regex test (alias of %match)

**Signature:** `%matches <string> <pattern:string> -> boolean`

Return true when a string matches a regular expression.

**Transform**

```odin
{out}
hit = %matches @.email "^[\\w.+-]+@[\\w-]+\\.[a-z]{2,}$"
miss = %matches @.bad "^[\\w.+-]+@[\\w-]+\\.[a-z]{2,}$"
```

**In**

```odin
email = "a@b.com"
bad = "not-an-email"
```

**Out**

```odin
{out}
hit = ?true
miss = ?false
```

**Notes**

- An exact alias of %match with identical, unanchored behavior. Two required arguments: the string and the regex pattern.
- Backslashes in the pattern are escaped within the ODIN string literal (\\w for \w). Returns a boolean (? prefix).
