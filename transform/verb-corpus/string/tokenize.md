# %tokenize

split text into tokens

**Signature:** `%tokenize <string> [delim:string] -> array`

Split text into an array of tokens, on whitespace by default.

**Transform**

```odin
{out}
out = %tokenize @.text
```

**In**

```odin
text = "the quick fox"
```

**Out**

```odin
{out}
{.out[] : ~}
"the"
"quick"
"fox"
```

**Notes**

- Returns an array, serialized as an ODIN array section. With no delimiter it splits on whitespace and drops empty tokens.
- Pass an optional delimiter to split on a specific character; tokens are trimmed when a delimiter is given.
