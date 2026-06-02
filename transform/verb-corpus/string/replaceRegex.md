# %replaceRegex

regex replace all

**Signature:** `%replaceRegex <string> <pattern:string> <replacement:string> -> string`

Replace every match of a regular expression with a replacement string.

**Transform**

```odin
{out}
hit = %replaceRegex @.text "[0-9]+" "#"
miss = %replaceRegex @.text "[xyz]+" "#"
bad = %replaceRegex @.text "([" "#"
```

**In**

```odin
text = "a1b22c333"
```

**Out**

```odin
{out}
hit = "a#b#c#"
miss = "a1b22c333"
bad = ~
```

**Notes**

- Three required arguments: string, regex pattern, replacement. The pattern is applied globally.
- Invalid or unsafe (ReDoS-prone) patterns return null rather than throwing. Use %replace for literal replacement.
