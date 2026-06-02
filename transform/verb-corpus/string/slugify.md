# %slugify

make a URL-safe slug

**Signature:** `%slugify <string> -> string`

Lowercase a string, strip punctuation, and join words with hyphens.

**Transform**

```odin
{out}
out = %slugify @.title
punct = %slugify @.messy
underscore = %slugify @.snake
```

**In**

```odin
title = "Hello World!"
messy = "  --Café & Tea--  "
snake = "hello_world_foo"
```

**Out**

```odin
{out}
out = "hello-world"
punct = "caf-tea"
underscore = "hello-world-foo"
```

**Notes**

- Non-word characters are removed, spaces and underscores become hyphens, repeated hyphens collapse, and leading/trailing hyphens are trimmed.
- Accented or non-ASCII letters are removed rather than transliterated; chain %stripAccents before %slugify to fold them to ASCII first.
