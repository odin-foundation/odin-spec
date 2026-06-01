<!-- GENERATED FILE — do not edit by hand.
     Source: sdk/golden/transform-corpus/  ·  Generator: sdk/typescript/scripts/build-transform-corpus.ts
     Every example below is executed by the golden runner; the Out block is exact engine output. -->

# Transform Cookbook

A field guide to authoring ODIN transforms, by example. Every card is **engine-verified**:
the **In** block is run through the transform engine and the **Out** block is its exact output.

## The mental model

A transform is an ODIN document that maps a **source** document to a **target** document.
ODIN is always the canonical model in between — even an `odin->odin` transform parses the
source into ODIN, applies the mapping, and serializes ODIN back out.

Every transform begins with a **direction header** declaring the source and target formats:

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "odin->odin"

{$source}
format = "odin"

{$target}
format = "odin"
```

The examples in this cookbook show only the **mapping block(s)** below that header — the
`odin->odin` header above is implied and is what the engine actually runs.

A mapping is a section header (`{out}`, `{invoice}`, `{policies[]}`) followed by
`target = expression` lines. Each right-hand expression is one of three things:

| Form | Meaning | Example |
|------|---------|---------|
| `@path` | **Copy** a value from the source | `name = @.name` |
| `%verb args...` | **Apply a verb** to compute a value | `name = %upper @.name` |
| `"..."` `##42` `#$9.99` | A **literal** value | `country = "US"` |

## ODIN-specific traps

These are the mistakes that look right but aren't. Each is demonstrated in the **Avoid**
list of the cards below.

- **A quoted string is reserved for an actual string.** Conditions and lookups are
  *verb-expressions*, not quoted infix. Write `%ifElse %gt @.amount ##1000 ...`, never
  `%ifElse "@.amount > 1000" ...` — the quoted form is just a literal string and the
  comparison never runs.
- **Bare words are literals, not paths.** `%upper name` uppercases the literal `"name"`.
  Read a field with `@.name`.
- **`@` copies, `%` computes, `"..."` is data.** Mixing these up is the most common error.
- **Reference coercion: `##@` / `#$@`.** Prefix a reference with a type to coerce on copy —
  `##@.count` copies `count` as an integer, `#$@.price` as currency.
- **Modifiers travel with the field.** Append `:required`, `:confidential`, `:deprecated`
  to a mapping to emit `!`, `*`, `-` prefixes in the output.
- **`:as` and relative paths in nested loops.** Alias a loop with `:as` and read inner
  elements with relative paths so nested iterations don't collide.
- **`${...}` interpolation is only valid inside `:literal` blocks.** Outside a literal
  segment, build strings with `%concat`.
- **Lookup tables: quote `"TABLE.column"`.** The first `%lookup` argument names the column
  and must be quoted; an unquoted `TABLE.column` parses as three tokens.

## Verb cards

One card per verb, grouped by family. Each card is engine-verified end to end.

## String

### `%camelCase` — convert to camelCase

Re-case a delimited or mixed-case string into camelCase.

**Signature:** `%camelCase <string> -> string`

**Transform**

```odin
{out}
out = %camelCase @.name
```

**In**

```odin
name = "hello-world"
```

**Out**

```odin
{out}
out = "helloWorld"
```

**Notes**

- Splits on hyphens, underscores, spaces, and case boundaries, then joins with the first word lowercased and the rest capitalized.

### `%capitalize` — capitalize the first letter

Uppercase the first character and lowercase the rest.

**Signature:** `%capitalize <string> -> string`

**Transform**

```odin
{out}
out = %capitalize @.word
```

**In**

```odin
word = "hONDA"
```

**Out**

```odin
{out}
out = "Honda"
```

**Notes**

- First character is uppercased and every following character is lowercased.
- Operates on the whole string as one word — use %titleCase for per-word capitalization.

**Avoid**

- `out = %capitalize honda` — bare word honda is read as the literal string, capitalizing the literal instead of the field; use @.word

### `%center` — center-pad to a width

Pad both sides of a string to center it within a target width.

**Signature:** `%center <string> <width:integer> [char:string] -> string`

**Transform**

```odin
{out}
out = %center @.hi ##6 "-"
```

**In**

```odin
hi = "hi"
```

**Out**

```odin
{out}
out = "--hi--"
```

**Notes**

- Two required arguments plus an optional fill character (default space). When the padding is odd, the extra character goes on the right.
- A string already at or above the width is returned unchanged.

### `%clean` — strip control chars and normalize whitespace

Remove control characters, normalize Unicode whitespace to spaces, collapse runs, and trim.

**Signature:** `%clean <string> -> string`

**Transform**

```odin
{out}
out = %clean @.raw
```

**In**

```odin
raw = "  hello   world  "
```

**Out**

```odin
{out}
out = "hello world"
```

**Notes**

- Single argument. Removes ASCII control characters, maps assorted Unicode spaces to a regular space, then collapses whitespace and trims.
- Use %normalizeSpace when only whitespace collapsing is needed.

### `%concat` — join strings end to end

Concatenate two or more values into a single string.

**Signature:** `%concat <string> <string> [<string>...] -> string`

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

- `fullName = %concat @.firstName @.lastName` — no separator argument yields "JohnSmith"; pass " " between the fields

### `%contains` — substring test

Return true when the string contains the given substring.

**Signature:** `%contains <string> <search:string> -> boolean`

**Transform**

```odin
{out}
out = %contains @.name "ond"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two arguments: the haystack string then the quoted needle.
- Returns a boolean (? prefix in ODIN output). The match is case-sensitive.

**Avoid**

- `out = %contains @.name ond` — bare word ond is not a quoted literal or path, so it is dropped and the engine raises a 2-argument arity error; quote the needle as "ond"

### `%endsWith` — suffix test

Return true when the string ends with the given suffix.

**Signature:** `%endsWith <string> <suffix:string> -> boolean`

**Transform**

```odin
{out}
out = %endsWith @.name "da"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two arguments: the string then the quoted suffix.
- Returns a boolean (? prefix in ODIN output). The match is case-sensitive.

### `%extract` — pull a regex capture group

Return a capture group from the first regex match in a string.

**Signature:** `%extract <string> <pattern:string> [group:integer] -> string`

**Transform**

```odin
{out}
out = %extract @.text "([0-9]+)" ##1
```

**In**

```odin
text = "order 42 placed"
```

**Out**

```odin
{out}
out = "42"
```

**Notes**

- Two required arguments plus an optional group index (default 0 = whole match, 1 = first capture group).
- Returns null when the pattern does not match or the group index is out of range.

**Avoid**

- `out = %extract "text" "([0-9]+)" ##1` — quoting the first argument as "text" matches against the literal word, which has no digits, so the result is null (~); use @.text to read the field

### `%formatPhone` — format a phone number by country

Strip non-digits and format a phone number for a given country code.

**Signature:** `%formatPhone <string> <country:string> -> string`

**Transform**

```odin
{out}
out = %formatPhone @.phone "US"
```

**In**

```odin
phone = "2125551234"
```

**Out**

```odin
{out}
out = "(212) 555-1234"
```

**Notes**

- Two required arguments: the raw number and a country code (US, CA, GB, DE, FR, AU, JP).
- Non-digit characters are stripped before formatting. A wrong digit count for the country returns the original value unchanged.

### `%join` — join array elements with a delimiter

Concatenate the elements of an array into a single string separated by a delimiter.

**Signature:** `%join <array> <delim:string> -> string`

**Transform**

```odin
{out}
out = %join @.tags ", "
```

**In**

```odin
tags[0] = "a"
tags[1] = "b"
tags[2] = "c"
```

**Out**

```odin
{out}
out = "a, b, c"
```

**Notes**

- First argument is an array reference; second is the quoted delimiter.
- An array source is supplied in ODIN with indexed assignments (tags[0], tags[1], ...).

### `%kebabCase` — convert to kebab-case

Re-case a delimited or mixed-case string into kebab-case.

**Signature:** `%kebabCase <string> -> string`

**Transform**

```odin
{out}
out = %kebabCase @.name
```

**In**

```odin
name = "helloWorld"
```

**Out**

```odin
{out}
out = "hello-world"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the lowercased words with hyphens.

### `%leftOf` — text before a delimiter

Return the portion of a string before the first occurrence of a delimiter.

**Signature:** `%leftOf <string> <delim:string> -> string`

**Transform**

```odin
{out}
out = %leftOf @.path "."
```

**In**

```odin
path = "a.b.c"
```

**Out**

```odin
{out}
out = "a"
```

**Notes**

- Two required arguments: the string and the delimiter.
- If the delimiter is not found, the original string is returned unchanged.

### `%length` — character count of a string

Return the number of characters in a string.

**Signature:** `%length <string> -> integer`

**Transform**

```odin
{out}
out = %length @.name
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = ##5
```

**Notes**

- Returns an integer (## prefix in ODIN output).
- Counts UTF-16 code units, matching the underlying string length.

### `%levenshtein` — edit distance between two strings

Return the minimum single-character edits to turn one string into another.

**Signature:** `%levenshtein <string> <string> -> integer`

**Transform**

```odin
{out}
out = %levenshtein @.a @.b
```

**In**

```odin
a = "kitten"
b = "sitting"
```

**Out**

```odin
{out}
out = ##3
```

**Notes**

- Two required string arguments. Counts insertions, deletions, and substitutions. Returns an integer (## prefix).
- Inputs longer than an internal length limit return null.

### `%lower` — lowercase a string

Lowercase every character of a string value.

**Signature:** `%lower <string> -> string`

**Transform**

```odin
{out}
out = %lower @.name
```

**In**

```odin
name = "HONDA"
```

**Out**

```odin
{out}
out = "honda"
```

**Notes**

- First and only argument is the string to lowercase.
- The argument must be a reference (@.name) or a quoted literal — a bare word is read as a literal string, not a path.

**Avoid**

- `out = %lower` — missing the source argument — the engine raises a verb-arity error
- `out = %lower name` — bare word name is read as the literal string "name", producing "name"; use @.name to read the field

### `%mask` — apply a formatting mask

Lay a string's characters into a template, inserting literal mask punctuation.

**Signature:** `%mask <string> <pattern:string> -> string`

**Transform**

```odin
{out}
out = %mask @.ssn "###-##-####"
```

**In**

```odin
ssn = "123456789"
```

**Out**

```odin
{out}
out = "123-45-6789"
```

**Notes**

- In the mask, the characters # A and * each consume one source character; any other character is emitted literally.
- Two required arguments: the source string and the mask pattern.

### `%match` — regex test

Return true when a string matches a regular expression.

**Signature:** `%match <string> <pattern:string> -> boolean`

**Transform**

```odin
{out}
out = %match @.code "^[A-Z]{3}$"
```

**In**

```odin
code = "ABC"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two required arguments: the string and the regex pattern. The pattern is tested (not anchored automatically).
- Returns a boolean (? prefix). Invalid or unsafe patterns return null.

### `%matches` — regex test (alias of %match)

Return true when a string matches a regular expression anywhere.

**Signature:** `%matches <string> <pattern:string> -> boolean`

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

### `%normalizeSpace` — collapse and trim whitespace

Collapse runs of whitespace to single spaces and trim the ends.

**Signature:** `%normalizeSpace <string> -> string`

**Transform**

```odin
{out}
out = %normalizeSpace @.raw
```

**In**

```odin
raw = "  a   b  "
```

**Out**

```odin
{out}
out = "a b"
```

**Notes**

- Single argument. Interior whitespace runs become one space and leading/trailing whitespace is removed.

### `%pad` — pad to a width (right side)

Pad the end of a string with a fill character until it reaches a target length.

**Signature:** `%pad <string> <length:integer> <char:string> -> string`

**Transform**

```odin
{out}
out = %pad @.code ##6 "."
```

**In**

```odin
code = "42"
```

**Out**

```odin
{out}
out = "42...."
```

**Notes**

- Pads on the right, behaving like %padRight.
- Three required arguments: string, target length, fill character; only the first character of the fill is used.

### `%padLeft` — left-pad to a width

Pad the start of a string with a fill character until it reaches a target length.

**Signature:** `%padLeft <string> <length:integer> <char:string> -> string`

**Transform**

```odin
{out}
out = %padLeft @.code ##6 "0"
```

**In**

```odin
code = "42"
```

**Out**

```odin
{out}
out = "000042"
```

**Notes**

- Three required arguments: string, target length, fill character.
- Only the first character of the fill argument is used. A string already at or above the length is returned unchanged.

**Avoid**

- `out = %padLeft @.code ##6` — omitting the fill-character argument fails the 3-argument requirement and yields null (~), not space-padding

### `%padRight` — right-pad to a width

Pad the end of a string with a fill character until it reaches a target length.

**Signature:** `%padRight <string> <length:integer> <char:string> -> string`

**Transform**

```odin
{out}
out = %padRight @.code ##6 "0"
```

**In**

```odin
code = "42"
```

**Out**

```odin
{out}
out = "420000"
```

**Notes**

- Three required arguments: string, target length, fill character.
- Only the first character of the fill argument is used. A string already at or above the length is returned unchanged.

**Avoid**

- `out = %padRight @.code ##6` — omitting the fill-character argument fails the 3-argument requirement and yields null (~)

### `%pascalCase` — convert to PascalCase

Re-case a delimited or mixed-case string into PascalCase.

**Signature:** `%pascalCase <string> -> string`

**Transform**

```odin
{out}
out = %pascalCase @.name
```

**In**

```odin
name = "hello-world"
```

**Out**

```odin
{out}
out = "HelloWorld"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the words with each first letter capitalized and no separator.

### `%repeat` — repeat a string N times

Concatenate a string with itself a given number of times.

**Signature:** `%repeat <string> <count:integer> -> string`

**Transform**

```odin
{out}
out = %repeat @.ab ##3
```

**In**

```odin
ab = "ab"
```

**Out**

```odin
{out}
out = "ababab"
```

**Notes**

- Two required arguments: the string and a non-negative repeat count.
- A count of 0 returns an empty string; a negative count returns null. The total length is capped by an internal limit.

### `%replace` — replace all literal occurrences

Replace every literal occurrence of a substring with a replacement.

**Signature:** `%replace <string> <find:string> <replacement:string> -> string`

**Transform**

```odin
{out}
out = %replace @.name "o" "0"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "H0nda"
```

**Notes**

- Three required arguments: string, literal find, literal replacement.
- Matching is literal (not regex) and replaces all occurrences. Use %replaceRegex for pattern matching.

**Avoid**

- `out = %replace @.name "o"` — missing the replacement argument — the engine raises a 3-argument arity error

### `%replaceRegex` — regex replace all

Replace every match of a regular expression with a replacement string.

**Signature:** `%replaceRegex <string> <pattern:string> <replacement:string> -> string`

**Transform**

```odin
{out}
out = %replaceRegex @.text "[0-9]+" "#"
```

**In**

```odin
text = "a1b22c333"
```

**Out**

```odin
{out}
out = "a#b#c#"
```

**Notes**

- Three required arguments: string, regex pattern, replacement. The pattern is applied globally.
- Invalid or unsafe (ReDoS-prone) patterns return null rather than throwing. Use %replace for literal replacement.

### `%reverseString` — reverse the characters

Return the string with its characters in reverse order.

**Signature:** `%reverseString <string> -> string`

**Transform**

```odin
{out}
out = %reverseString @.name
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "adnoH"
```

**Notes**

- Single argument. Reversal is Unicode-aware (code points, not UTF-16 units).

### `%rightOf` — text after a delimiter

Return the portion of a string after the first occurrence of a delimiter.

**Signature:** `%rightOf <string> <delim:string> -> string`

**Transform**

```odin
{out}
out = %rightOf @.path "."
```

**In**

```odin
path = "a.b.c"
```

**Out**

```odin
{out}
out = "b.c"
```

**Notes**

- Two required arguments: the string and the delimiter. Only the first delimiter is split on, so the remainder may still contain it.
- If the delimiter is not found, an empty string is returned.

### `%slugify` — make a URL-safe slug

Lowercase a string, strip punctuation, and join words with hyphens.

**Signature:** `%slugify <string> -> string`

**Transform**

```odin
{out}
out = %slugify @.title
```

**In**

```odin
title = "Hello World!"
```

**Out**

```odin
{out}
out = "hello-world"
```

**Notes**

- Non-word characters are removed, spaces and underscores become hyphens, repeated hyphens collapse, and leading/trailing hyphens are trimmed.

### `%snakeCase` — convert to snake_case

Re-case a delimited or mixed-case string into snake_case.

**Signature:** `%snakeCase <string> -> string`

**Transform**

```odin
{out}
out = %snakeCase @.name
```

**In**

```odin
name = "helloWorld"
```

**Out**

```odin
{out}
out = "hello_world"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the lowercased words with underscores.

### `%soundex` — phonetic code

Return the 4-character Soundex code for a word.

**Signature:** `%soundex <string> -> string`

**Transform**

```odin
{out}
out = %soundex @.name
```

**In**

```odin
name = "Robert"
```

**Out**

```odin
{out}
out = "R163"
```

**Notes**

- Single argument. Keeps the first letter, encodes the rest, and pads to four characters.
- Similar-sounding names share a code (Robert and Rupert both yield R163).

### `%split` — split and select one element

Split a string on a delimiter and return the element at a given index.

**Signature:** `%split <string> <delim:string> <index:integer> -> string`

**Transform**

```odin
{out}
out = %split @.csv "," ##1
```

**In**

```odin
csv = "a,b,c"
```

**Out**

```odin
{out}
out = "b"
```

**Notes**

- Three required arguments: string, delimiter, then the zero-based index to select.
- Negative indices count from the end. Out-of-range indices return null. Use %tokenize to get the whole array.

**Avoid**

- `out = %split @.csv ","` — omitting the index argument fails the 3-argument requirement and yields null (~); %split returns a single element, not the array

### `%startsWith` — prefix test

Return true when the string begins with the given prefix.

**Signature:** `%startsWith <string> <prefix:string> -> boolean`

**Transform**

```odin
{out}
out = %startsWith @.name "Hon"
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = ?true
```

**Notes**

- Two arguments: the string then the quoted prefix.
- Returns a boolean (? prefix in ODIN output). The match is case-sensitive.

### `%stripAccents` — remove diacritics

Strip accents and combining marks, leaving base ASCII letters.

**Signature:** `%stripAccents <string> -> string`

**Transform**

```odin
{out}
out = %stripAccents @.name
```

**In**

```odin
name = "café"
```

**Out**

```odin
{out}
out = "cafe"
```

**Notes**

- Single argument. Uses Unicode NFD decomposition, then removes combining diacritical marks.

### `%substring` — extract by start and length

Return the slice of a string starting at an index for a given length.

**Signature:** `%substring <string> <start:integer> <length:integer> -> string`

**Transform**

```odin
{out}
out = %substring @.name ##1 ##3
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "ond"
```

**Notes**

- Three required arguments: string, zero-based start index, then length (not an end index).
- All three are required — a two-argument call returns null.

**Avoid**

- `out = %substring @.name ##3 ##1` — second arg is start and third is length, not start/end; ##3 ##1 starts at index 3 for length 1 → "d", not "ond"

### `%titleCase` — capitalize each word

Lowercase the string, then uppercase the first letter of every whitespace-separated word.

**Signature:** `%titleCase <string> -> string`

**Transform**

```odin
{out}
out = %titleCase @.phrase
```

**In**

```odin
phrase = "honda civic type r"
```

**Out**

```odin
{out}
out = "Honda Civic Type R"
```

**Notes**

- Words are split on whitespace; each word's first character is uppercased and the remainder lowercased.
- Use %capitalize to title-case only the very first letter of the whole string.

### `%tokenize` — split text into tokens

Split text into an array of tokens, on whitespace by default.

**Signature:** `%tokenize <string> [delim:string] -> array`

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

### `%trim` — strip leading and trailing whitespace

Remove whitespace from both ends of a string.

**Signature:** `%trim <string> -> string`

**Transform**

```odin
{out}
out = %trim @.raw
```

**In**

```odin
raw = "  hello  "
```

**Out**

```odin
{out}
out = "hello"
```

**Notes**

- Trims both ends; interior whitespace is left untouched.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trim` — missing the source argument — the engine raises a verb-arity error

### `%trimLeft` — strip leading whitespace

Remove whitespace from the start of a string only.

**Signature:** `%trimLeft <string> -> string`

**Transform**

```odin
{out}
out = %trimLeft @.raw
```

**In**

```odin
raw = "  hello  "
```

**Out**

```odin
{out}
out = "hello  "
```

**Notes**

- Only leading whitespace is removed; trailing whitespace is preserved.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trimLeft` — missing the source argument — the engine raises a verb-arity error

### `%trimRight` — strip trailing whitespace

Remove whitespace from the end of a string only.

**Signature:** `%trimRight <string> -> string`

**Transform**

```odin
{out}
out = %trimRight @.raw
```

**In**

```odin
raw = "  hello  "
```

**Out**

```odin
{out}
out = "  hello"
```

**Notes**

- Only trailing whitespace is removed; leading whitespace is preserved.
- The argument must be a reference (@.raw) or a quoted literal.

**Avoid**

- `out = %trimRight` — missing the source argument — the engine raises a verb-arity error

### `%truncate` — cut a string to a max length

Return at most the first N characters of a string.

**Signature:** `%truncate <string> <length:integer> -> string`

**Transform**

```odin
{out}
out = %truncate @.name ##3
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "Hon"
```

**Notes**

- Two required arguments: string and max length.
- No ellipsis is appended — the result is a plain slice of the first N characters.

### `%upper` — uppercase a string

Uppercase every character of a string value.

**Signature:** `%upper <string> -> string`

**Transform**

```odin
{out}
upper = %upper @.name
```

**In**

```odin
name = "honda"
```

**Out**

```odin
{out}
upper = "HONDA"
```

**Notes**

- First and only argument is the string to uppercase.
- The argument must be a reference (@.name) or a quoted literal — a bare word is read as a literal string, not a path.

**Avoid**

- `upper = %upper` — missing the source argument — the engine raises a verb-arity error
- `upper = %upper name` — bare word name is read as the literal string "name", producing "NAME"; use @.name to read the field

### `%wordCount` — count words

Count the whitespace-separated words in a string.

**Signature:** `%wordCount <string> -> integer`

**Transform**

```odin
{out}
out = %wordCount @.text
```

**In**

```odin
text = "the quick brown fox"
```

**Out**

```odin
{out}
out = ##4
```

**Notes**

- Single argument. Returns an integer (## prefix). Empty or whitespace-only input yields 0.

### `%wrap` — word-wrap to a width

Insert newlines at word boundaries so each line fits within a width.

**Signature:** `%wrap <string> <width:integer> -> string`

**Transform**

```odin
{out}
out = %wrap @.text ##10
```

**In**

```odin
text = "the quick brown fox"
```

**Out**

```odin
{out}
out = "the quick\nbrown fox"
```

**Notes**

- Two required arguments: the string and the line width. Wrapping happens at whitespace; lines are joined with newline characters.
- A width of zero or less returns null; a string already within the width is returned unchanged.

## Numeric

### `%abs` — absolute value

Return the absolute value of a number.

**Signature:** `%abs <number> -> number`

**Transform**

```odin
{out}
v = %abs @.a
```

**In**

```odin
a = #-7.25
```

**Out**

```odin
{out}
v = #7.25
```

**Notes**

- A fractional input stays a number (#); a whole-valued result would emit as an integer (##).

**Avoid**

- `v = %abs` — the value argument is required; omitting it raises a verb-arity error

### `%add` — add two numbers

Add two numeric values and return the sum.

**Signature:** `%add <number> <number> -> number`

**Transform**

```odin
{out}
total = %add @.base @.fee
```

**In**

```odin
base = ##500
fee = ##75
```

**Out**

```odin
{out}
total = ##575
```

**Notes**

- Both integer inputs produce an integer (##) result.
- Numeric-looking strings are coerced before adding.

**Avoid**

- `total = %add @.base` — %add needs exactly two arguments; one argument raises a verb-arity error

### `%ceil` — round up to integer

Round a number up toward positive infinity, returning an integer.

**Signature:** `%ceil <number> -> integer`

**Transform**

```odin
{out}
v = %ceil @.a
```

**In**

```odin
a = #3.2
```

**Out**

```odin
{out}
v = ##4
```

**Notes**

- Always returns an integer (##), even for a fractional input.
- Rounds toward positive infinity, so -3.7 would ceil to -3.

### `%convertUnit` — unit conversion

Convert a value between two compatible units of the same family.

**Signature:** `%convertUnit <number> <string:fromUnit> <string:toUnit> -> number`

**Transform**

```odin
{out}
v = %convertUnit @.a "kg" "lb"
```

**In**

```odin
a = #100
```

**Out**

```odin
{out}
v = #220.462442
```

**Notes**

- Supports mass, length, volume, speed, area, data, time, and temperature (C/F/K) families.
- Results are rounded to at most six decimal places.
- Incompatible units across families (e.g. kg → km) yield null (~).

**Avoid**

- `v = %convertUnit @.a "kg" "km"` — the units belong to different families (mass vs length), so the conversion yields null (~)

### `%divide` — quotient of two numbers

Divide the first number by the second.

**Signature:** `%divide <number:a> <number:b> -> number`

**Transform**

```odin
{out}
v = %divide @.a @.b
```

**In**

```odin
a = #7
b = #2
```

**Out**

```odin
{out}
v = #3.5
```

**Notes**

- Argument order is a ÷ b.
- Division always yields a number type (#), reflecting the possibility of a decimal result.
- A zero divisor yields null (~) rather than an error; use %safeDivide to supply a fallback.

**Avoid**

- `v = %divide @.a` — two operands are required; one operand raises a verb-arity error

### `%floor` — round down to integer

Round a number down toward negative infinity, returning an integer.

**Signature:** `%floor <number> -> integer`

**Transform**

```odin
{out}
v = %floor @.a
```

**In**

```odin
a = #3.7
```

**Out**

```odin
{out}
v = ##3
```

**Notes**

- Always returns an integer (##), even for a fractional input.
- Rounds toward negative infinity, so -3.2 would floor to -4 (use %trunc to round toward zero).

### `%formatCurrency` — two-decimal currency string

Format a number as a currency string with exactly two decimal places.

**Signature:** `%formatCurrency <number> -> string`

**Transform**

```odin
{out}
v = %formatCurrency @.a
```

**In**

```odin
a = #19.5
```

**Out**

```odin
{out}
v = "19.50"
```

**Notes**

- Always emits two decimals (19.5 → "19.50") and produces a plain string with no currency symbol.
- The output is a string, not the #$ currency value type.

### `%formatInteger` — floor to integer string

Format a number as an integer string by flooring it.

**Signature:** `%formatInteger <number> -> string`

**Transform**

```odin
{out}
v = %formatInteger @.a
```

**In**

```odin
a = #42.9
```

**Out**

```odin
{out}
v = "42"
```

**Notes**

- Flooring discards the fraction (42.9 → 42), it does not round.
- The result is a string, not a number type.

### `%formatLocaleNumber` — locale-grouped number string

Format a number using locale-specific grouping and decimal separators.

**Signature:** `%formatLocaleNumber <number> [string:locale] -> string`

**Transform**

```odin
{out}
v = %formatLocaleNumber @.a "en-US"
```

**In**

```odin
a = #1234.56
```

**Out**

```odin
{out}
v = "1,234.56"
```

**Notes**

- Pass an explicit locale (e.g. "en-US", "de-DE") for deterministic grouping; "en-US" groups thousands with commas.
- Omitting the locale uses the host system default, so always supply one for reproducible output.
- The result is a string.

**Avoid**

- `v = %formatLocaleNumber @.a` — without an explicit locale the host default locale is used, making the grouping/separator output environment-dependent

### `%formatNumber` — fixed decimal-place string

Format a number as a string with a fixed number of decimal places.

**Signature:** `%formatNumber <number> <integer:decimals> -> string`

**Transform**

```odin
{out}
v = %formatNumber @.a ##3
```

**In**

```odin
a = #3.14159
```

**Out**

```odin
{out}
v = "3.142"
```

**Notes**

- The result is a string (quoted), not a number — trailing/rounded decimals are fixed by toFixed.
- The decimals argument is required.

**Avoid**

- `v = %formatNumber @.a` — the decimals argument is required; omitting it raises a verb-arity error

### `%formatPercent` — fraction to percentage string

Format a fractional ratio as a percentage string with a fixed number of decimals.

**Signature:** `%formatPercent <number> <integer:decimals> -> string`

**Transform**

```odin
{out}
v = %formatPercent @.a ##1
```

**In**

```odin
a = #0.1234
```

**Out**

```odin
{out}
v = "12.3%"
```

**Notes**

- Multiplies by 100 and appends a percent sign (0.1234 → "12.3%").
- The result is a string.

**Avoid**

- `v = %formatPercent @.a` — the engine requires the decimals argument; omitting it raises a verb-arity error even though it reads like an optional parameter

### `%isFinite` — finite-number test

Test whether a numeric value is finite (not Infinity or NaN).

**Signature:** `%isFinite <number> -> boolean`

**Transform**

```odin
{out}
v = %isFinite @.a
```

**In**

```odin
a = #3.5
```

**Out**

```odin
{out}
v = ?true
```

**Notes**

- Returns a boolean (?).
- Non-numeric input types yield false rather than an error.

### `%isNaN` — NaN test

Test whether a numeric value is NaN.

**Signature:** `%isNaN <number> -> boolean`

**Transform**

```odin
{out}
v = %isNaN @.a
```

**In**

```odin
a = #3.5
```

**Out**

```odin
{out}
v = ?false
```

**Notes**

- Returns a boolean (?).
- Non-numeric input types yield false rather than an error.

### `%maxOf` — maximum of scalar arguments

Return the largest of two or more scalar numeric arguments.

**Signature:** `%maxOf <number> <number> ... -> number`

**Transform**

```odin
{out}
v = %maxOf @.a @.b @.c
```

**In**

```odin
a = #9
b = #4
c = #7
```

**Out**

```odin
{out}
v = ##9
```

**Notes**

- Variadic: pass each operand as a separate scalar argument.
- This is not an array-reducing verb — to take the maximum of an array, fan its elements out as individual references.

**Avoid**

- `v = %maxOf @.list` — %maxOf compares its scalar arguments; passing a single array reference coerces the array to its length, not its maximum element

### `%minOf` — minimum of scalar arguments

Return the smallest of two or more scalar numeric arguments.

**Signature:** `%minOf <number> <number> ... -> number`

**Transform**

```odin
{out}
v = %minOf @.a @.b @.c
```

**In**

```odin
a = #9
b = #4
c = #7
```

**Out**

```odin
{out}
v = ##4
```

**Notes**

- Variadic: pass each operand as a separate scalar argument.
- This is not an array-reducing verb — to take the minimum of an array, fan its elements out as individual references.

**Avoid**

- `v = %minOf @.list` — %minOf compares its scalar arguments; passing a single array reference coerces the array to its length, not its minimum element

### `%mod` — remainder of division

Return the remainder of dividing the first number by the second.

**Signature:** `%mod <number:a> <number:b> -> number`

**Transform**

```odin
{out}
v = %mod @.a @.b
```

**In**

```odin
a = #17
b = #5
```

**Out**

```odin
{out}
v = ##2
```

**Notes**

- Argument order is a mod b.
- A zero divisor yields null (~) rather than an error.

**Avoid**

- `v = %mod @.a` — two operands are required; one operand raises a verb-arity error

### `%multiply` — product of two numbers

Multiply two numbers.

**Signature:** `%multiply <number:a> <number:b> -> number`

**Transform**

```odin
{out}
v = %multiply @.a @.b
```

**In**

```odin
a = #6
b = #7
```

**Out**

```odin
{out}
v = ##42
```

**Notes**

- A whole-valued product emits as an integer (##); a fractional product emits as a number (#).

**Avoid**

- `v = %multiply @.a` — two operands are required; one operand raises a verb-arity error

### `%negate` — arithmetic negation

Return the additive inverse of a number.

**Signature:** `%negate <number> -> number`

**Transform**

```odin
{out}
v = %negate @.a
```

**In**

```odin
a = #8
```

**Out**

```odin
{out}
v = ##-8
```

**Notes**

- A whole-valued result emits as an integer (##).

**Avoid**

- `v = %negate` — the value argument is required; omitting it raises a verb-arity error

### `%parseInt` — parse string to integer with radix

Parse a string into an integer using an explicit numeric base.

**Signature:** `%parseInt <string> [integer:radix] -> integer`

**Transform**

```odin
{out}
v = %parseInt @.a ##16
```

**In**

```odin
a = "FF"
```

**Out**

```odin
{out}
v = ##255
```

**Notes**

- The radix is optional and defaults to 10; here base 16 parses "FF" to 255.
- A radix must be between 2 and 36, otherwise the result is null (~).

**Avoid**

- `v = %parseInt @.a` — without a radix the default base 10 is used, so a hex string like "FF" parses to null (~) rather than 255

### `%random` — seeded random number in range

Generate a deterministic random integer within an inclusive range using a string seed.

**Signature:** `%random <number:min> <number:max> [string:seed] -> integer`

**Transform**

```odin
{out}
v = %random ##1 ##10 "seed123"
```

**In**

```odin
x = #0
```

**Out**

```odin
{out}
v = ##2
```

**Notes**

- With min and max supplied, the result is an integer in the inclusive range [min, max].
- A string seed makes the result deterministic; the same seed always yields the same value.
- Without a seed the result is non-deterministic, so corpus usage should always pass a seed.

**Avoid**

- `v = %random ##1 ##10` — without a seed the generator is non-deterministic; supply a string seed for reproducible output

### `%round` — round to N decimal places

Round a number to a fixed number of decimal places.

**Signature:** `%round <number> <integer:places> -> number`

**Transform**

```odin
{out}
amount = %round @.amount ##2
```

**In**

```odin
amount = #123.456789
```

**Out**

```odin
{out}
amount = #123.46
```

**Notes**

- The second argument is the decimal-place count and must be an integer literal (##2).
- A fractional input stays a number (#) in the output.

**Avoid**

- `amount = %round @.amount` — the decimal-places argument is required; omitting it raises a verb-arity error

### `%safeDivide` — division with zero fallback

Divide two numbers, returning a supplied default when the divisor is zero or invalid.

**Signature:** `%safeDivide <number:numerator> <number:denominator> <default> -> number`

**Transform**

```odin
{out}
v = %safeDivide @.a @.b ##0
```

**In**

```odin
a = #10
b = #0
```

**Out**

```odin
{out}
v = ##0
```

**Notes**

- When the denominator is 0, NaN, or Infinity, the third argument (the default) is returned as-is.
- Avoids the verbose %ifElse %eq ... %divide pattern.

**Avoid**

- `v = %safeDivide @.a @.b` — the default value is a required third argument; omitting it raises a verb-arity error

### `%sign` — sign of a number

Return -1, 0, or 1 according to the sign of a number.

**Signature:** `%sign <number> -> integer`

**Transform**

```odin
{out}
v = %sign @.a
```

**In**

```odin
a = #-3.5
```

**Out**

```odin
{out}
v = ##-1
```

**Notes**

- Returns an integer (##): -1 for negatives, 1 for positives, 0 for zero.

### `%subtract` — difference of two numbers

Subtract the second number from the first.

**Signature:** `%subtract <number:a> <number:b> -> number`

**Transform**

```odin
{out}
v = %subtract @.a @.b
```

**In**

```odin
a = #10
b = #3
```

**Out**

```odin
{out}
v = ##7
```

**Notes**

- Argument order is a − b.
- A whole-valued result emits as an integer (##); a fractional result emits as a number (#).

**Avoid**

- `v = %subtract @.a` — two operands are required; one operand raises a verb-arity error

### `%switch` — multi-way value mapping

Map an input to a result by matching value/result pairs, with a trailing default.

**Signature:** `%switch <value> <case> <result> ... [default] -> any`

**Transform**

```odin
{out}
v = %switch @.a "1" "one" "2" "two" "other"
```

**In**

```odin
a = "2"
```

**Out**

```odin
{out}
v = "two"
```

**Notes**

- Arguments after the value are case/result pairs; an odd trailing argument is the default.
- Matching is done on the string form of the value.

**Avoid**

- `v = %switch @.a` — at least one case/result pair (two args after the value) is required; the value alone raises a verb-arity error

### `%trunc` — truncate toward zero

Discard the fractional part of a number, rounding toward zero.

**Signature:** `%trunc <number> -> integer`

**Transform**

```odin
{out}
v = %trunc @.a
```

**In**

```odin
a = #-3.7
```

**Out**

```odin
{out}
v = ##-3
```

**Notes**

- Rounds toward zero (-3.7 → -3), unlike %floor which rounds toward negative infinity (-3.7 → -4).
- Always returns an integer (##).

## Date & Time

### `%addDays` — shift a date by whole days

Add (or subtract) a number of days to a date, returning an ISO date.

**Signature:** `%addDays <date> <integer:days> -> string`

**Transform**

```odin
{out}
d = %addDays @.start ##7
```

**In**

```odin
start = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-06-22"
```

**Notes**

- The day count is a fractional value floored to a whole number of days.
- A negative count moves the date backward.

**Avoid**

- `d = %addDays @.start "7"` — the count should be a numeric token; a quoted string is coerced but obscures intent

### `%addHours` — shift a timestamp by hours

Add (or subtract) hours to a timestamp, returning an ISO date-time.

**Signature:** `%addHours <timestamp> <number:hours> -> string`

**Transform**

```odin
{out}
t = %addHours @.ts ##5
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
```

**Out**

```odin
{out}
t = "2024-06-15T15:30:00.000Z"
```

**Notes**

- Output is a full ISO timestamp with milliseconds and a UTC suffix.
- A negative count moves the timestamp earlier.

### `%addMinutes` — shift a timestamp by minutes

Add (or subtract) minutes to a timestamp, returning an ISO date-time.

**Signature:** `%addMinutes <timestamp> <number:minutes> -> string`

**Transform**

```odin
{out}
t = %addMinutes @.ts ##90
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
```

**Out**

```odin
{out}
t = "2024-06-15T12:00:00.000Z"
```

**Notes**

- Output is a full ISO timestamp with milliseconds and a UTC suffix.
- A negative count moves the timestamp earlier.

### `%addMonths` — shift a date by whole months

Add (or subtract) a number of months to a date, returning an ISO date.

**Signature:** `%addMonths <date> <integer:months> -> string`

**Transform**

```odin
{out}
d = %addMonths @.start ##2
```

**In**

```odin
start = 2024-01-31
```

**Out**

```odin
{out}
d = "2024-03-31"
```

**Notes**

- Month arithmetic uses calendar-month stepping, not a fixed day count.
- A negative count moves the date backward.

### `%addSeconds` — shift a timestamp by seconds

Add (or subtract) seconds to a timestamp, returning an ISO date-time.

**Signature:** `%addSeconds <timestamp> <number:seconds> -> string`

**Transform**

```odin
{out}
t = %addSeconds @.ts ##45
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
```

**Out**

```odin
{out}
t = "2024-06-15T10:30:45.000Z"
```

**Notes**

- Output is a full ISO timestamp with milliseconds and a UTC suffix.
- A negative count moves the timestamp earlier.

### `%addYears` — shift a date by whole years

Add (or subtract) a number of years to a date, returning an ISO date.

**Signature:** `%addYears <date> <integer:years> -> string`

**Transform**

```odin
{out}
d = %addYears @.start ##1
```

**In**

```odin
start = 2024-02-29
```

**Out**

```odin
{out}
d = "2025-03-01"
```

**Notes**

- A leap-day input rolls forward to March 1 in a non-leap target year.
- A negative count moves the date backward.

### `%ageFromDate` — completed years between two dates

Compute age in complete years from a birth date as of a reference date.

**Signature:** `%ageFromDate <date:birth> <date:asOf> -> integer`

**Transform**

```odin
{out}
n = %ageFromDate @.birth @.asOf
```

**In**

```odin
birth = 1990-06-15
asOf = 2024-06-15
```

**Out**

```odin
{out}
n = ##34
```

**Notes**

- Only complete years are counted; a birthday not yet reached this year is excluded.
- Provide the as-of date explicitly for a deterministic result; omitting it uses the current date.

**Avoid**

- `n = %ageFromDate @.birth` — without an as-of date the result depends on the current clock and is not reproducible

### `%businessDays` — add weekday-only days

Shift a date by N business days, skipping Saturdays and Sundays.

**Signature:** `%businessDays <date> <integer:count> -> string`

**Transform**

```odin
{out}
d = %businessDays @.start ##5
```

**In**

```odin
start = 2024-01-15
```

**Out**

```odin
{out}
d = "2024-01-22"
```

**Notes**

- Weekends are skipped; there is no holiday calendar.
- A negative count subtracts business days.

### `%dateDiff` — difference between two dates in a unit

Measure the gap from the first date to the second in days, months, or years.

**Signature:** `%dateDiff <date> <date> <string:unit> -> integer`

**Transform**

```odin
{out}
d = %dateDiff @.a @.b "days"
```

**In**

```odin
a = 2024-01-01
b = 2024-03-01
```

**Out**

```odin
{out}
d = ##60
```

**Notes**

- The unit literal must be one of "days", "months", or "years".
- The result is the second date minus the first, so b before a yields a negative count.

**Avoid**

- `d = %dateDiff @.a @.b "weeks"` — "weeks" is not a supported unit; only days, months, and years are accepted

### `%dayOfWeek` — ISO weekday number

Return the ISO 8601 weekday of a date (Monday=1 through Sunday=7).

**Signature:** `%dayOfWeek <date> -> integer`

**Transform**

```odin
{out}
n = %dayOfWeek @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
n = ##6
```

**Notes**

- Uses ISO numbering: Monday is 1 and Sunday is 7.
- June 15, 2024 is a Saturday, hence 6.

### `%daysBetweenDates` — whole days from start to end

Count the whole days between two dates, signed by direction.

**Signature:** `%daysBetweenDates <date:start> <date:end> -> integer`

**Transform**

```odin
{out}
n = %daysBetweenDates @.a @.b
```

**In**

```odin
a = 2024-01-01
b = 2024-01-15
```

**Out**

```odin
{out}
n = ##14
```

**Notes**

- Result is end minus start; an earlier end yields a negative count.
- A focused alternative to %dateDiff with the "days" unit.

### `%endOfDay` — raise a timestamp to the last instant of its day

Set a timestamp to the final moment of its day (23:59:59.999).

**Signature:** `%endOfDay <timestamp> -> string`

**Transform**

```odin
{out}
t = %endOfDay @.ts
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "2024-06-15T23:59:59.999Z"
```

**Notes**

- Day boundaries are computed in UTC.
- The result carries 999 milliseconds.

### `%endOfMonth` — last day of the month

Advance a date to the final day of its calendar month.

**Signature:** `%endOfMonth <date> -> string`

**Transform**

```odin
{out}
d = %endOfMonth @.date
```

**In**

```odin
date = 2024-02-15
```

**Out**

```odin
{out}
d = "2024-02-29"
```

**Notes**

- Leap years are honored — February 2024 ends on the 29th.
- Returns an ISO date string.

### `%endOfYear` — December 31 of the year

Advance a date to the last day of its calendar year.

**Signature:** `%endOfYear <date> -> string`

**Transform**

```odin
{out}
d = %endOfYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-12-31"
```

**Notes**

- Returns an ISO date string (month and day set to 12-31).
- Year boundaries are computed in UTC.

### `%formatDate` — render a date with a pattern

Format an ISO date string using a display pattern.

**Signature:** `%formatDate <date> <string:pattern> -> string`

**Transform**

```odin
{out}
date = %formatDate @.date "MM/DD/YYYY"
```

**In**

```odin
date = "2024-06-15"
```

**Out**

```odin
{out}
date = "06/15/2024"
```

**Notes**

- The pattern is a quoted literal (tokens MM, DD, YYYY).
- An unparseable input date yields ~ (null) rather than an error.

**Avoid**

- `date = %formatDate @.date` — the pattern argument is required; omitting it raises a verb-arity error
- `date = %formatDate @.date MM/DD/YYYY` — the pattern must be quoted; an unquoted pattern is not a single string token

### `%formatDuration` — human-readable duration

Render a duration in seconds (or ISO 8601) as English components.

**Signature:** `%formatDuration <integer:seconds> -> string`

**Transform**

```odin
{out}
s = %formatDuration @.elapsed
```

**In**

```odin
elapsed = ##90061
```

**Out**

```odin
{out}
s = "1 day, 1 hour, 1 minute, 1 second"
```

**Notes**

- A numeric input is treated as seconds and expanded into days, hours, minutes, and seconds.
- An ISO 8601 duration string such as "PT2H30M" is also accepted; zero components are omitted.

### `%formatTime` — render the time portion of a timestamp

Format the clock portion of a timestamp using a display pattern.

**Signature:** `%formatTime <timestamp> <string:pattern> -> string`

**Transform**

```odin
{out}
t = %formatTime @.ts "HH:mm:ss"
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "14:30:45"
```

**Notes**

- HH is 24-hour; use hh with A for a 12-hour clock.
- Time components are read in UTC.

### `%formatTimestamp` — render a full timestamp with a pattern

Format both date and time portions of a timestamp using a display pattern.

**Signature:** `%formatTimestamp <timestamp> <string:pattern> -> string`

**Transform**

```odin
{out}
t = %formatTimestamp @.ts "YYYY-MM-DD HH:mm:ss"
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "2024-06-15 14:30:45"
```

**Notes**

- Combines date tokens (YYYY, MM, DD) and time tokens (HH, mm, ss) in one pattern.
- A timestamp without a zone offset is interpreted as UTC.

### `%fromUnix` — epoch seconds to ISO timestamp

Convert a Unix epoch value to an ISO timestamp string.

**Signature:** `%fromUnix <integer:epoch> -> string`

**Transform**

```odin
{out}
t = %fromUnix @.epoch
```

**In**

```odin
epoch = ##1718447400
```

**Out**

```odin
{out}
t = "2024-06-15T10:30:00.000Z"
```

**Notes**

- Seconds and milliseconds are auto-detected by magnitude.
- Output is a full ISO timestamp with a UTC suffix.

### `%isAfter` — chronological greater-than

Test whether the first date follows the second.

**Signature:** `%isAfter <date> <date> -> boolean`

**Transform**

```odin
{out}
b = %isAfter @.a @.b
```

**In**

```odin
a = 2024-06-15
b = 2024-01-01
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Comparison is strict; equal dates yield false.
- Both arguments are interpreted in UTC.

### `%isBefore` — chronological less-than

Test whether the first date precedes the second.

**Signature:** `%isBefore <date> <date> -> boolean`

**Transform**

```odin
{out}
b = %isBefore @.a @.b
```

**In**

```odin
a = 2024-01-01
b = 2024-06-15
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Comparison is strict; equal dates yield false.
- Both arguments are interpreted in UTC.

### `%isBetween` — inclusive range test

Test whether a date falls within a start/end range, inclusive of both ends.

**Signature:** `%isBetween <date> <date:start> <date:end> -> boolean`

**Transform**

```odin
{out}
b = %isBetween @.d @.start @.end
```

**In**

```odin
d = 2024-06-15
start = 2024-01-01
end = 2024-12-31
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Both bounds are inclusive — a date equal to start or end returns true.
- Requires all three date arguments.

**Avoid**

- `b = %isBetween @.d @.start` — the end bound is required; only two dates raises a verb-arity error

### `%isLeapYear` — leap-year test

Report whether the year of a date is a leap year.

**Signature:** `%isLeapYear <date> -> boolean`

**Transform**

```odin
{out}
b = %isLeapYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Applies the full Gregorian rule (divisible by 4, except centuries not divisible by 400).
- 2024 is a leap year.

### `%isValidDate` — strict format-and-value check

Validate that a string matches a date format and names a real calendar date.

**Signature:** `%isValidDate <string:value> <string:format> -> boolean`

**Transform**

```odin
{out}
b = %isValidDate @.raw "YYYY-MM-DD"
```

**In**

```odin
raw = "2024-02-29"
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Checks both the literal format and that the day exists — Feb 29 is valid only in leap years.
- Supported formats are YYYY-MM-DD, MM/DD/YYYY, DD/MM/YYYY, and YYYY/MM/DD.

### `%nextBusinessDay` — next weekday after a date

Return the first Monday-Friday date strictly after the input.

**Signature:** `%nextBusinessDay <date> -> string`

**Transform**

```odin
{out}
d = %nextBusinessDay @.start
```

**In**

```odin
start = 2024-01-19
```

**Out**

```odin
{out}
d = "2024-01-22"
```

**Notes**

- Always advances at least one day; a Friday rolls forward to Monday.
- Weekends are skipped; there is no holiday calendar.

### `%parseDate` — parse a string into an ISO date

Read a date from a formatted string and normalize it to YYYY-MM-DD.

**Signature:** `%parseDate <string:value> <string:pattern> -> string`

**Transform**

```odin
{out}
d = %parseDate @.raw "MM/DD/YYYY"
```

**In**

```odin
raw = "06/15/2024"
```

**Out**

```odin
{out}
d = "2024-06-15"
```

**Notes**

- The pattern locates each component by position (MM, DD, YYYY).
- Output is always an ISO date string regardless of the input pattern.

**Avoid**

- `d = %parseDate @.raw` — the pattern argument is required; omitting it raises a verb-arity error

### `%parseTimestamp` — parse a string into an ISO timestamp

Read a timestamp from a formatted string and normalize it to ISO date-time.

**Signature:** `%parseTimestamp <string:value> <string:pattern> -> string`

**Transform**

```odin
{out}
t = %parseTimestamp @.raw "YYYY-MM-DD HH:mm:ss"
```

**In**

```odin
raw = "2024-06-15 14:30:45"
```

**Out**

```odin
{out}
t = "2024-06-15T14:30:45"
```

**Notes**

- Each component is located by its position in the pattern.
- Output is an ISO date-time string without a zone suffix.

### `%quarter` — calendar quarter of a date

Return the calendar quarter (1-4) that a date falls in.

**Signature:** `%quarter <date> -> integer`

**Transform**

```odin
{out}
n = %quarter @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
n = ##2
```

**Notes**

- Q1 is Jan-Mar, Q2 Apr-Jun, Q3 Jul-Sep, Q4 Oct-Dec.
- June falls in Q2.

### `%startOfDay` — floor a timestamp to midnight

Reset a timestamp to the very start of its day (00:00:00.000).

**Signature:** `%startOfDay <timestamp> -> string`

**Transform**

```odin
{out}
t = %startOfDay @.ts
```

**In**

```odin
ts = 2024-06-15T14:30:45Z
```

**Out**

```odin
{out}
t = "2024-06-15T00:00:00.000Z"
```

**Notes**

- Day boundaries are computed in UTC.
- Output is a full ISO timestamp.

### `%startOfMonth` — first day of the month

Collapse a date to the first day of its calendar month.

**Signature:** `%startOfMonth <date> -> string`

**Transform**

```odin
{out}
d = %startOfMonth @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-06-01"
```

**Notes**

- Returns an ISO date string (day set to 01).
- Month boundaries are computed in UTC.

### `%startOfYear` — January 1 of the year

Collapse a date to the first day of its calendar year.

**Signature:** `%startOfYear <date> -> string`

**Transform**

```odin
{out}
d = %startOfYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
d = "2024-01-01"
```

**Notes**

- Returns an ISO date string (month and day set to 01-01).
- Year boundaries are computed in UTC.

### `%toUnix` — timestamp to epoch seconds

Convert a timestamp to whole seconds since the Unix epoch.

**Signature:** `%toUnix <timestamp> -> integer`

**Transform**

```odin
{out}
n = %toUnix @.ts
```

**In**

```odin
ts = 2024-06-15T10:30:00Z
```

**Out**

```odin
{out}
n = ##1718447400
```

**Notes**

- Result is seconds (not milliseconds) since 1970-01-01T00:00:00Z, floored.
- Pairs with %fromUnix for the reverse conversion.

### `%weekOfYear` — ISO week number

Return the ISO 8601 week number (1-53) for a date.

**Signature:** `%weekOfYear <date> -> integer`

**Transform**

```odin
{out}
n = %weekOfYear @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
n = ##24
```

**Notes**

- Follows ISO 8601 week numbering, anchored on the Thursday of each week.
- Weeks near year boundaries may belong to the adjacent year.

## Array

### `%at` — element at an index

Read a single array element by index, supporting negative indices.

**Signature:** `%at <array> <integer:index> -> value`

**Transform**

```odin
{out}
last = %at @.items ##-1
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
last = "c"
```

**Notes**

- Negative indices count from the end (##-1 is the last element).
- Out-of-range indices return null (~).

### `%chunk` — split into fixed-size groups

Break an array into sub-arrays of a given size.

**Signature:** `%chunk <array> <integer:size> -> array`

**Transform**

```odin
{out}
pages = %chunk @.items ##2
```

**In**

```odin
{items[] : ~}
##1
##2
##3
##4
##5
```

**Out**

```odin
{out}
{.pages[] : [0], [1]}
##1, ##2
##3, ##4
##5,
```

**Notes**

- Each chunk is a positional pair/tuple block; a trailing partial chunk keeps remaining elements.
- A size of zero or less returns null.

### `%compact` — drop empty values

Remove null and empty-string elements from an array.

**Signature:** `%compact <array> -> array`

**Transform**

```odin
{out}
clean = %compact @.values
```

**In**

```odin
{values[] : ~}
##1
~
##2
```

**Out**

```odin
{out}
{.clean[] : ~}
##1
##2
```

**Notes**

- Removes null (~) and empty-string elements; zero and false are kept.

### `%concatArrays` — join two arrays

Append the second array to the first, preserving order.

**Signature:** `%concatArrays <array> <array> -> array`

**Transform**

```odin
{out}
combined = %concatArrays @.a @.b
```

**In**

```odin
{a[] : ~}
"x"
"y"
{b[] : ~}
"z"
```

**Out**

```odin
{out}
{.combined[] : ~}
"x"
"y"
"z"
```

**Notes**

- Both arguments are arrays; the result is their concatenation.

### `%cumprod` — running product

Return the cumulative product at each position.

**Signature:** `%cumprod <array> -> array`

**Transform**

```odin
{out}
growth = %cumprod @.factors
```

**In**

```odin
{factors[] : ~}
#2
#3
#4
```

**Out**

```odin
{out}
{.growth[] : ~}
##2
##6
##24
```

**Notes**

- Each element is the product of all values up to and including that position.

### `%cumsum` — running total

Return the cumulative sum at each position.

**Signature:** `%cumsum <array> -> array`

**Transform**

```odin
{out}
running = %cumsum @.values
```

**In**

```odin
{values[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
{.running[] : ~}
##10
##30
##60
```

**Notes**

- Each element is the sum of all values up to and including that position.

### `%dedupe` — remove duplicate records by key

Drop later records that repeat a key-field value, keeping the first.

**Signature:** `%dedupe <array> <string:keyField> -> array`

**Transform**

```odin
{out}
uniq = %dedupe @.records "id"
```

**In**

```odin
records = "[{\"id\": \"r1\", \"name\": \"first\"}, {\"id\": \"r1\", \"name\": \"dup\"}, {\"id\": \"r2\", \"name\": \"second\"}]"
```

**Out**

```odin
{out}
{.uniq[] : id, name}
"r1", "first"
"r2", "second"
```

**Notes**

- Unlike %distinct, it compares one key field, not the whole value.
- The record array is supplied as a JSON-array string so the key field reads as a plain value.

**Avoid**

- `{records[] : id, name} → "r1", "first" → "r1", "dup"` — a typed ODIN object-array block wraps each field, so the key field is not found and no rows are deduplicated; supply the records as a JSON-array string literal instead

### `%diff` — consecutive differences

Return the difference between each element and the prior one.

**Signature:** `%diff <array> [<integer:periods>] -> array`

**Transform**

```odin
{out}
changes = %diff @.values
```

**In**

```odin
{values[] : ~}
##10
##15
##12
```

**Out**

```odin
{out}
{.changes[] : ~}
~
##5
##-3
```

**Notes**

- The first element is null; later elements are value minus the prior value.
- An optional periods argument compares against N positions back.

### `%distinct` — remove duplicate values

Drop repeated values from a primitive array, keeping first occurrence.

**Signature:** `%distinct <array> [<string:field>] -> array`

**Transform**

```odin
{out}
uniq = %distinct @.tags
```

**In**

```odin
{tags[] : ~}
"auto"
"home"
"auto"
```

**Out**

```odin
{out}
{.uniq[] : ~}
"auto"
"home"
```

**Notes**

- With no field argument it deduplicates primitive values.
- A second quoted field argument deduplicates an object array by that field.

### `%drop` — skip first N elements

Return the array with the first count elements removed.

**Signature:** `%drop <array> <integer:count> -> array`

**Transform**

```odin
{out}
rest = %drop @.items ##2
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
"d"
```

**Out**

```odin
{out}
{.rest[] : ~}
"c"
"d"
```

**Notes**

- The complement of %take: drops the leading count elements.

### `%every` — do all items match a condition

Return true when every record satisfies a field condition.

**Signature:** `%every <array> <string:field> <string:op> <value> -> boolean`

**Transform**

```odin
{out}
allActive = %every @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "active"
```

**Out**

```odin
{out}
allActive = ?true
```

**Notes**

- Field, operator, and value are three separate quoted arguments.
- An empty array returns ?true (vacuous truth).

### `%fillMissing` — replace null values

Substitute a value for null/missing array elements.

**Signature:** `%fillMissing <array> [<value:fill>] [<string:strategy>] -> array`

**Transform**

```odin
{out}
filled = %fillMissing @.values ##0
```

**In**

```odin
{values[] : ~}
##1
~
##3
```

**Out**

```odin
{out}
{.filled[] : ~}
##1
##0
##3
```

**Notes**

- The default strategy replaces each null with the supplied value.
- A third argument selects "forward", "backward", or "mean" filling instead.

### `%filter` — keep array elements matching a condition

Filter an array of objects to the rows where a field matches a value.

**Signature:** `%filter <array> <string:field> <string:op> <value> -> array`

**Transform**

```odin
{out}
active = %filter @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "cancelled"
"comprehensive", "active"
```

**Out**

```odin
{out}
{.active[] : type, status}
"liability", "active"
"comprehensive", "active"
```

**Notes**

- The field, operator, and value are three separate quoted arguments — not one condition string.
- The kept rows are emitted as a relative tabular sub-block ({.active[] : ...}) under the output section.

**Avoid**

- `active = %filter @.coverages "status = active"` — the condition is three arguments (field, op, value), not one quoted infix string; a single string raises a verb-arity error

### `%find` — first record matching a condition

Return the first record where a field matches, or null.

**Signature:** `%find <array> <string:field> <string:op> <value> -> object`

**Transform**

```odin
{out}
{.first} = %find @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "cancelled"
"collision", "active"
```

**Out**

```odin
{out}
{.first}
type = "collision"
status = "active"
```

**Notes**

- Returns a single object, emitted as a relative sub-section ({.first}).
- No match returns null (~).

### `%findIndex` — index of first matching record

Return the index of the first record matching a condition, or -1.

**Signature:** `%findIndex <array> <string:field> <string:op> <value> -> integer`

**Transform**

```odin
{out}
idx = %findIndex @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "cancelled"
"collision", "active"
```

**Out**

```odin
{out}
idx = ##1
```

**Notes**

- Indices are zero-based; no match returns ##-1.

### `%flatten` — flatten nested arrays one level

Collapse an array of arrays into a single array, one level deep.

**Signature:** `%flatten <array> -> array`

**Transform**

```odin
{out}
all = %flatten @.nested
```

**In**

```odin
nested = "[[1, 2], [3, 4]]"
```

**Out**

```odin
{out}
{.all[] : ~}
##1
##2
##3
##4
```

**Notes**

- A nested (2D) array is supplied as a JSON-array string literal; the engine parses it into nested arrays before flattening.
- Flattening is one level deep only — deeper nesting is preserved.

### `%groupBy` — group records by a field

Bucket an object array into { key, items } groups by a field value.

**Signature:** `%groupBy <array> <string:field> -> array`

**Transform**

```odin
{out}
groups = %groupBy @.orders "status"
```

**In**

```odin
{orders[] : id, status}
"o1", "active"
"o2", "pending"
"o3", "active"
```

**Out**

```odin
{out}
{out.groups[0]}
key = "active"
{.items[] : id, status}
"o1", "active"
"o3", "active"
{out.groups[1]}
key = "pending"
{.items[] : id, status}
"o2", "pending"
```

**Notes**

- Each group is an object with a key and an items array.
- Groups appear in first-seen key order.

### `%includes` — membership test

Return true when an array contains a value.

**Signature:** `%includes <array> <value> -> boolean`

**Transform**

```odin
{out}
hasCA = %includes @.states "CA"
```

**In**

```odin
{states[] : ~}
"NY"
"CA"
"TX"
```

**Out**

```odin
{out}
hasCA = ?true
```

**Notes**

- Compares by string value, so it works on typed primitive arrays.

### `%indexOf` — first index of a value

Return the position of a value in an array, or -1 if absent.

**Signature:** `%indexOf <array> <value> -> integer`

**Transform**

```odin
{out}
pos = %indexOf @.nums ##20
```

**In**

```odin
nums = "[10, 20, 30]"
```

**Out**

```odin
{out}
pos = ##1
```

**Notes**

- Indices are zero-based; a missing value yields ##-1.
- The array is given as a JSON-array string so its elements compare as plain values.

**Avoid**

- `{nums[] : ~} → ##10 → ##20 → ##30` — a typed ODIN array block wraps each element, so equality never matches and %indexOf always returns ##-1; supply the array as a JSON-array string literal instead

### `%join` — join an array into a string

Concatenate array elements into one string with a separator.

**Signature:** `%join <array> <string:separator> -> string`

**Transform**

```odin
{out}
tags = %join @.tags ", "
```

**In**

```odin
{tags[] : ~}
"insurance"
"auto"
"policy"
```

**Out**

```odin
{out}
tags = "insurance, auto, policy"
```

**Notes**

- The source array is written as an ODIN primitive-array block: {tags[] : ~} with one value per line.
- The separator is a required quoted literal.

**Avoid**

- `tags = %join @.tags` — the separator argument is required; omitting it raises a verb-arity error

### `%lag` — previous value at each position

Shift values forward so each row sees the earlier value.

**Signature:** `%lag <array> [<integer:periods>] [<value:default>] -> array`

**Transform**

```odin
{out}
prev = %lag @.prices
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
{.prev[] : ~}
~
##10
##20
```

**Notes**

- Leading positions use the default (null unless a third argument is given).
- Equivalent to looking back one position by default.

### `%lead` — next value at each position

Shift values backward so each row sees the following value.

**Signature:** `%lead <array> [<integer:periods>] [<value:default>] -> array`

**Transform**

```odin
{out}
next = %lead @.prices
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
{.next[] : ~}
##20
##30
~
```

**Notes**

- Trailing positions use the default (null unless a third argument is given).
- Equivalent to looking ahead one position by default.

### `%limit` — first N items

Return the first count items (alias of %take).

**Signature:** `%limit <array> <integer:count> -> array`

**Transform**

```odin
{out}
top2 = %limit @.results ##2
```

**In**

```odin
{results[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
{.top2[] : ~}
"a"
"b"
```

**Notes**

- Alias for %take; useful for pagination or capping result sets.

### `%map` — extract one field from each record

Project a single field out of every object into a flat array.

**Signature:** `%map <array> <string:field> -> array`

**Transform**

```odin
{out}
types = %map @.coverages "type"
```

**In**

```odin
{coverages[] : type, premium}
"liability", ##100
"collision", ##300
```

**Out**

```odin
{out}
{.types[] : ~}
"liability"
"collision"
```

**Notes**

- Returns a primitive array of the chosen field values, in source order.

### `%partition` — split by a condition

Divide an array into [matching, non-matching] by a field condition.

**Signature:** `%partition <array> <string:field> <string:op> <value> -> array`

**Transform**

```odin
{out}
split = %partition @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "cancelled"
```

**Out**

```odin
{out}
{.split[] : [0].type, .status}
"liability", "active"
"collision", "cancelled"
```

**Notes**

- Returns a two-element array: index 0 is matches, index 1 is the rest.
- Field, operator, and value are three separate quoted arguments.

### `%pctChange` — consecutive percentage change

Return the fractional change between each element and the prior one.

**Signature:** `%pctChange <array> [<integer:periods>] -> array`

**Transform**

```odin
{out}
returns = %pctChange @.prices
```

**In**

```odin
{prices[] : ~}
##100
##110
##99
```

**Out**

```odin
{out}
{.returns[] : ~}
~
#0.1
#-0.1
```

**Notes**

- The first element is null; results are fractions (0.1 = +10%).
- A prior value of zero yields null to avoid division by zero.

### `%pivot` — records to a keyed object

Reshape an array of records into one object keyed by a field.

**Signature:** `%pivot <array> <string:keyField> <string:valueField> -> object`

**Transform**

```odin
{out}
{.lookup} = %pivot @.items "name" "value"
```

**In**

```odin
{items[] : name, value}
"a", ##1
"b", ##2
```

**Out**

```odin
{out}
{.lookup}
a = ##1
b = ##2
```

**Notes**

- Keys come from keyField and values from valueField; duplicate keys keep the last value.
- The result is an object, emitted as a relative sub-section ({.lookup}).

### `%pluck` — extract a field from each record

Collect one field value from every object into an array.

**Signature:** `%pluck <array> <string:field> -> array`

**Transform**

```odin
{out}
ids = %pluck @.users "id"
```

**In**

```odin
{users[] : id, name}
"u1", "alice"
"u2", "bob"
```

**Out**

```odin
{out}
{.ids[] : ~}
"u1"
"u2"
```

**Notes**

- Behaves like %map: projects the named field into a flat array.

### `%range` — generate a numeric sequence

Produce an array of integers from start to end (exclusive).

**Signature:** `%range <integer:start> <integer:end> [<integer:step>] -> array`

**Transform**

```odin
{out}
nums = %range ##1 ##5
```

**In**

```odin
{doc}
x = "y"
```

**Out**

```odin
{out}
{.nums[] : ~}
##1
##2
##3
##4
```

**Notes**

- End is exclusive: range ##1 ##5 yields 1,2,3,4.
- Takes literal numeric arguments rather than a source array; an optional third argument is the step.

### `%rank` — assign ranks to values

Tag each element with a _rank field (ties share a rank).

**Signature:** `%rank <array> [<string:field>] [<string:direction>] -> array`

**Transform**

```odin
{out}
ranked = %rank @.scores
```

**In**

```odin
scores = "[10, 30, 20]"
```

**Out**

```odin
{out}
{.ranked[] : _rank, value}
##3, ##10
##1, ##30
##2, ##20
```

**Notes**

- Default direction is descending, so the largest value is rank 1.
- The array is supplied as a JSON-array string so the added _rank field surfaces in the output.

**Avoid**

- `{scores[] : ~} → ##10 → ##30 → ##20` — a typed ODIN array block wraps each element, so the added _rank field is dropped from the serialized output; supply the array as a JSON-array string literal instead

### `%reduce` — fold an array with a verb

Combine all elements into one value using a named verb and a seed.

**Signature:** `%reduce <array> <string:verb> <value:initial> -> value`

**Transform**

```odin
{out}
total = %reduce @.prices "add" ##0
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
total = ##60
```

**Notes**

- The verb name is a quoted string; it is applied as verb(accumulator, element).
- The third argument is the initial accumulator and the result for an empty array.

**Avoid**

- `total = %reduce @.prices %add ##0` — the fold verb must be a quoted name ("add"), not a nested %add call; the %add form is parsed as its own verb and raises an arity error

### `%reverse` — reverse array order

Return the array with element order reversed.

**Signature:** `%reverse <array> -> array`

**Transform**

```odin
{out}
rev = %reverse @.items
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
{.rev[] : ~}
"c"
"b"
"a"
```

**Notes**

- Reverses in place order; the original array is not mutated.

### `%rowNumber` — add sequential row numbers

Tag each element with a 1-based _rowNum field.

**Signature:** `%rowNumber <array> -> array`

**Transform**

```odin
{out}
numbered = %rowNumber @.names
```

**In**

```odin
names = "[\"alice\", \"bob\"]"
```

**Out**

```odin
{out}
{.numbered[] : _rowNum, value}
##1, "alice"
##2, "bob"
```

**Notes**

- Primitive elements are wrapped as { _rowNum, value } objects.
- The array is supplied as a JSON-array string so the added _rowNum field surfaces in the output.

**Avoid**

- `{names[] : ~} → "alice" → "bob"` — a typed ODIN array block wraps each element, so the added _rowNum field is dropped from the serialized output; supply the array as a JSON-array string literal instead

### `%sample` — random subset

Draw N elements at random, optionally seeded for reproducibility.

**Signature:** `%sample <array> <integer:count> [<integer:seed>] -> array`

**Transform**

```odin
{out}
picked = %sample @.items ##2 ##42
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
"d"
"e"
```

**Out**

```odin
{out}
{.picked[] : ~}
"d"
"c"
```

**Notes**

- A seed makes the draw deterministic; the same seed always returns the same subset.
- Without a seed the result varies per run (seeded here for a stable example).

### `%shift` — offset elements by N positions

Move elements forward (or backward) filling gaps with null.

**Signature:** `%shift <array> [<integer:periods>] [<value:fill>] -> array`

**Transform**

```odin
{out}
lagged = %shift @.prices ##1
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
{.lagged[] : ~}
~
##10
##20
```

**Notes**

- Positive periods shift toward later indices; leading gaps become null (~).
- A negative period shifts backward; an optional third argument sets the fill value.

### `%slice` — sub-range of an array

Return elements from a start index up to (not including) an end index.

**Signature:** `%slice <array> <integer:start> [<integer:end>] -> array`

**Transform**

```odin
{out}
mid = %slice @.items ##1 ##3
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
"d"
```

**Out**

```odin
{out}
{.mid[] : ~}
"b"
"c"
```

**Notes**

- End is exclusive: start ##1 end ##3 yields indices 1 and 2.
- Omitting end slices to the end of the array.

### `%some` — does any item match a condition

Return true when at least one record satisfies a field condition.

**Signature:** `%some <array> <string:field> <string:op> <value> -> boolean`

**Transform**

```odin
{out}
hasCancelled = %some @.coverages "status" "=" "cancelled"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "cancelled"
```

**Out**

```odin
{out}
hasCancelled = ?true
```

**Notes**

- Field, operator, and value are three separate quoted arguments.
- An empty array returns ?false.

### `%sort` — sort a primitive array ascending

Order array values ascending (numeric when all numbers, else lexical).

**Signature:** `%sort <array> [<string:field>] [<string:direction>] -> array`

**Transform**

```odin
{out}
sorted = %sort @.scores
```

**In**

```odin
{scores[] : ~}
##30
##10
##20
```

**Out**

```odin
{out}
{.sorted[] : ~}
##10
##20
##30
```

**Notes**

- Numeric arrays sort numerically; mixed/string arrays sort lexically.
- Optional second/third args sort an object array by field and direction ("asc"/"desc").

### `%sortBy` — sort an object array by a field

Order an array of records ascending by one named field.

**Signature:** `%sortBy <array> <string:field> -> array`

**Transform**

```odin
{out}
ranked = %sortBy @.coverages "premium"
```

**In**

```odin
{coverages[] : type, premium}
"collision", ##300
"liability", ##100
"comprehensive", ##200
```

**Out**

```odin
{out}
{.ranked[] : type, premium}
"liability", ##100
"comprehensive", ##200
"collision", ##300
```

**Notes**

- The field name is a required quoted argument.
- Sorts ascending; use %sort with a "desc" third argument for descending order.

**Avoid**

- `ranked = %sortBy @.coverages` — the field argument is required; omitting it raises a verb-arity error

### `%sortDesc` — sort a primitive array descending

Order array values from highest to lowest.

**Signature:** `%sortDesc <array> -> array`

**Transform**

```odin
{out}
sorted = %sortDesc @.scores
```

**In**

```odin
{scores[] : ~}
##10
##30
##20
```

**Out**

```odin
{out}
{.sorted[] : ~}
##30
##20
##10
```

**Notes**

- Takes only the array — no field argument; it sorts the values directly.

### `%take` — first N elements

Return the first count elements of an array.

**Signature:** `%take <array> <integer:count> -> array`

**Transform**

```odin
{out}
first2 = %take @.items ##2
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
"d"
```

**Out**

```odin
{out}
{.first2[] : ~}
"a"
"b"
```

**Notes**

- A count larger than the array returns the whole array.

### `%unique` — remove duplicate values

Drop repeated values from an array (alias of %distinct).

**Signature:** `%unique <array> [<string:field>] -> array`

**Transform**

```odin
{out}
uniq = %unique @.tags
```

**In**

```odin
{tags[] : ~}
"auto"
"home"
"auto"
```

**Out**

```odin
{out}
{.uniq[] : ~}
"auto"
"home"
```

**Notes**

- Alias for %distinct; keeps the first occurrence of each value.

### `%unpivot` — object to key/value records

Reshape an object into an array of { key, value } records.

**Signature:** `%unpivot <object> <string:keyName> <string:valueName> -> array`

**Transform**

```odin
{out}
rows = %unpivot @.lookup "key" "value"
```

**In**

```odin
{lookup}
a = ##1
b = ##2
```

**Out**

```odin
{out}
{.rows[] : key, value}
"a", ##1
"b", ##2
```

**Notes**

- The inverse of %pivot; iteration follows the object insertion order.
- The two quoted arguments name the key and value fields in each output record.

### `%zip` — pair two arrays element-wise

Combine two arrays into an array of positional pairs.

**Signature:** `%zip <array> <array> -> array`

**Transform**

```odin
{out}
paired = %zip @.names @.scores
```

**In**

```odin
{names[] : ~}
"alice"
"bob"
{scores[] : ~}
##90
##80
```

**Out**

```odin
{out}
{.paired[] : [0], [1]}
"alice", ##90
"bob", ##80
```

**Notes**

- Each result element is a [first, second] pair, emitted as a positional tabular block.
- Stops at the length of the shorter array.

## Logic & Conditionals

### `%and` — boolean AND

Return true only when both conditions are truthy.

**Signature:** `%and <a:boolean> <b:boolean> -> boolean`

**Transform**

```odin
{out}
both = %and %gte @.age ##18 %lt @.age ##65
```

**In**

```odin
age = ##30
```

**Out**

```odin
{out}
both = ?true
```

**Notes**

- Each operand is itself a verb-expression that yields a boolean (%gte …, %lt …).
- Exactly two operands; nest %and inside %and for three or more conditions.

**Avoid**

- `both = %and "@.a" "@.b"` — quoted operands are plain strings, and a string is only truthy when it spells "true"/"yes"/"y"/"1"; an arbitrary string like "@.a" is falsy, so this returns ?false regardless of @.a. Pass real boolean verb-expressions, not quoted paths.

### `%assert` — validate a condition

Return the condition value when truthy, otherwise null, for inline validation.

**Signature:** `%assert <condition:boolean> [<message:string>] -> value`

**Transform**

```odin
{out}
valid = %assert %gt @.amount ##0
```

**In**

```odin
amount = ##150
```

**Out**

```odin
{out}
valid = ?true
```

**Notes**

- Passes the condition through when truthy; a failing assertion yields null (~) and the transform continues.
- An optional second string argument is a diagnostic message and does not appear in the output.

**Avoid**

- `valid = %assert "@.amount > 0"` — the quoted infix string is a literal, and a non-empty arbitrary string is not one of the truthy tokens, so the assertion fails and yields null. Pass a real condition like %gt @.amount ##0.

### `%between` — inclusive range test

Return true when a value falls within [min, max] inclusive.

**Signature:** `%between <value:number> <min:number> <max:number> -> boolean`

**Transform**

```odin
{out}
inRange = %between @.age ##18 ##65
```

**In**

```odin
age = ##42
```

**Out**

```odin
{out}
inRange = ?true
```

**Notes**

- Takes three arguments: value, lower bound, upper bound — both bounds inclusive.
- Operands are coerced to numbers before comparison.

**Avoid**

- `inRange = %between @.age ##18` — %between requires all three arguments (value, min, max); supplying only two fails with a 'requires 3 arguments' error rather than defaulting the upper bound.

### `%coalesce` — first non-null value

Return the first argument that is not null or empty.

**Signature:** `%coalesce <value> [<value>...] -> value`

**Transform**

```odin
{out}
displayName = %coalesce @.nickname @.firstName "Guest"
```

**In**

```odin
nickname = ~
firstName = "Alice"
```

**Out**

```odin
{out}
displayName = "Alice"
```

**Notes**

- Arguments are tried left-to-right; the first non-null wins (here nickname is ~, so firstName is used).
- End with a quoted literal default so the result is never null.

**Avoid**

- `displayName = %coalesce @.nickname @.firstName` — with no literal fallback the result is ~ when every field is null

### `%cond` — multi-branch conditional

Evaluate condition/value pairs and return the value of the first true condition, else a default.

**Signature:** `%cond <cond1:boolean> <val1> [<cond2:boolean> <val2> ...] <default> -> value`

**Transform**

```odin
{out}
code = %cond %eq @.status "active" "A" %eq @.status "pending" "P" "X"
```

**In**

```odin
status = "pending"
```

**Out**

```odin
{out}
code = "P"
```

**Notes**

- Arguments are read as condition/value pairs left-to-right; the first truthy condition wins.
- A trailing odd argument is the default returned when no condition matches.

**Avoid**

- `code = %cond %eq @.status "active" "A"` — with an even argument count there is no trailing default, so when the single condition is false %cond returns null (~) instead of a fallback. Always append a default value.

### `%eq` — equality test

Return true when two values are equal, as a boolean usable as a condition.

**Signature:** `%eq <a:value> <b:value> -> boolean`

**Transform**

```odin
{out}
same = %eq @.status "active"
```

**In**

```odin
status = "active"
```

**Out**

```odin
{out}
same = ?true
```

**Notes**

- Both arguments are verb-expressions or paths/literals — never a quoted infix like "@.status == 'active'".
- The boolean result drops straight into a condition slot, e.g. %ifElse %eq @.status "active" ... .

**Avoid**

- `match = %eq @.code "200"` — an integer source code (##200) compares equal to the string "200": cross-type numeric/string operands are coerced before comparison, so this fires even though one side is text. Quote only when both sides are genuinely strings.

### `%gt` — greater than

Return true when the first value is strictly greater than the second.

**Signature:** `%gt <a:value> <b:value> -> boolean`

**Transform**

```odin
{out}
big = %gt @.amount ##1000
```

**In**

```odin
amount = ##2500
```

**Out**

```odin
{out}
big = ?true
```

**Notes**

- Numeric operands compare numerically; two strings compare lexically.
- This is the canonical condition builder for %ifElse, %and, %or, and %assert.

**Avoid**

- `big = %gt "@.amount > 1000"` — %gt needs two separate operands; a single quoted infix string is just one argument, so the transform fails with a 'requires 2 arguments' error. Write %gt @.amount ##1000.

### `%gte` — greater than or equal

Return true when the first value is greater than or equal to the second.

**Signature:** `%gte <a:value> <b:value> -> boolean`

**Transform**

```odin
{out}
ok = %gte @.score ##60
```

**In**

```odin
score = ##60
```

**Out**

```odin
{out}
ok = ?true
```

**Notes**

- Inclusive at the boundary: 60 >= 60 is true.
- Combine with %lt for inclusive-lower, exclusive-upper bands.

### `%ifElse` — choose a value on a condition

Return one of two values depending on a boolean condition.

**Signature:** `%ifElse <condition> <then> <else> -> value`

**Transform**

```odin
{out}
tier = %ifElse %gt @.amount ##1000 "VIP" "Standard"
```

**In**

```odin
amount = ##2500
```

**Out**

```odin
{out}
tier = "VIP"
```

**Notes**

- The condition is a verb-expression (%gt @.amount ##1000), evaluated left-to-right — not a quoted infix string.
- ifElse calls nest: the else branch can be another %ifElse for multi-way classification.

**Avoid**

- `tier = %ifElse "@.amount > 1000" "VIP" "Standard"` — a quoted string is a literal, always truthy-or-not as data — it is never parsed as a condition; the comparison silently never fires. Use %gt @.amount ##1000

### `%ifEmpty` — fallback when empty string

Return a fallback value when the input is an empty string, otherwise the input.

**Signature:** `%ifEmpty <value> <fallback> -> value`

**Transform**

```odin
{out}
label = %ifEmpty @.label "untitled"
```

**In**

```odin
label = ""
```

**Out**

```odin
{out}
label = "untitled"
```

**Notes**

- Triggers only on the empty string ""; a null (~) is not empty here — use %ifNull for null.
- Whitespace-only strings are not empty; trim first with %trim if you want that behavior.

### `%ifNull` — fallback when null

Return a fallback value when the input is null, otherwise the input.

**Signature:** `%ifNull <value> <fallback> -> value`

**Transform**

```odin
{out}
name = %ifNull @.nickname "Anonymous"
```

**In**

```odin
nickname = ~
```

**Out**

```odin
{out}
name = "Anonymous"
```

**Notes**

- Only null (~) triggers the fallback; an empty string "" passes through unchanged — use %ifEmpty for that.
- Like %coalesce but with exactly one value and one fallback.

### `%isArray` — array-type test

Return true when a value is an array.

**Signature:** `%isArray <value> -> boolean`

**Transform**

```odin
{out}
isList = %isArray @.tags
```

**In**

```odin
{tags[] : ~}
"a"
"b"
```

**Out**

```odin
{out}
isList = ?true
```

**Notes**

- The source declares the array with a {tags[] : ~} block of one value per line.
- Distinguishes arrays from objects; use %isObject for the latter.

### `%isBoolean` — boolean-type test

Return true when a value is of boolean type.

**Signature:** `%isBoolean <value> -> boolean`

**Transform**

```odin
{out}
isBool = %isBoolean @.active
```

**In**

```odin
active = ?true
```

**Out**

```odin
{out}
isBool = ?true
```

**Notes**

- Tests the type only; the string "true" is a string, not a boolean.
- Use %coerceBoolean to turn truthy strings into real booleans.

### `%isDate` — date-type test

Return true when a value is a date or timestamp type.

**Signature:** `%isDate <value> -> boolean`

**Transform**

```odin
{out}
isDt = %isDate %coerceDate @.born
```

**In**

```odin
born = "2020-01-15"
```

**Out**

```odin
{out}
isDt = ?true
```

**Notes**

- True for both date and timestamp types.
- An ISO string arriving as text is a string, not a date — %coerceDate promotes it to a real date first.

**Avoid**

- `isDt = %isDate @.born` — when born arrives as the plain string "2020-01-15" its type is string, so %isDate returns ?false despite looking date-like. Coerce with %coerceDate before testing.

### `%isNull` — null test

Return true when a value is null (~).

**Signature:** `%isNull <value> -> boolean`

**Transform**

```odin
{out}
missing = %isNull @.middleName
```

**In**

```odin
middleName = ~
```

**Out**

```odin
{out}
missing = ?true
```

**Notes**

- Only the null type (~) is null; an empty string "" is not null — use %ifEmpty for that.
- Useful as a guard condition before defaulting with %ifNull.

### `%isNumber` — numeric-type test

Return true when a value is integer, number, or currency.

**Signature:** `%isNumber <value> -> boolean`

**Transform**

```odin
{out}
isNum = %isNumber @.amount
```

**In**

```odin
amount = ##42
```

**Out**

```odin
{out}
isNum = ?true
```

**Notes**

- True for integer (##), number (#), and currency (#$) values alike.
- A numeric string like "42" is a string, not a number — coerce with %coerceNumber first if needed.

### `%isObject` — object-type test

Return true when a value is a nested object.

**Signature:** `%isObject <value> -> boolean`

**Transform**

```odin
{out}
isObj = %isObject @.address
```

**In**

```odin
{address}
city = "Reno"
```

**Out**

```odin
{out}
isObj = ?true
```

**Notes**

- The source nests fields under an {address} section, producing an object value.
- Complements %isArray for telling structured values apart.

### `%isString` — string-type test

Return true when a value is of string type.

**Signature:** `%isString <value> -> boolean`

**Transform**

```odin
{out}
isText = %isString @.name
```

**In**

```odin
name = "Ada"
```

**Out**

```odin
{out}
isText = ?true
```

**Notes**

- Tests the value's type, not its content; a numeric ##5 returns ?false.
- Pair with %typeOf when you need the type name rather than a yes/no answer.

### `%lt` — less than

Return true when the first value is strictly less than the second.

**Signature:** `%lt <a:value> <b:value> -> boolean`

**Transform**

```odin
{out}
small = %lt @.amount ##1000
```

**In**

```odin
amount = ##250
```

**Out**

```odin
{out}
small = ?true
```

**Notes**

- Mirror of %gt; numbers compare numerically and two strings compare lexically.
- Pair with %gte to build half-open ranges.

### `%lte` — less than or equal

Return true when the first value is less than or equal to the second.

**Signature:** `%lte <a:value> <b:value> -> boolean`

**Transform**

```odin
{out}
ok = %lte @.score ##100
```

**In**

```odin
score = ##100
```

**Out**

```odin
{out}
ok = ?true
```

**Notes**

- Inclusive at the boundary: 100 <= 100 is true.
- Mirror of %gte.

### `%ne` — inequality test

Return true when two values are not equal.

**Signature:** `%ne <a:value> <b:value> -> boolean`

**Transform**

```odin
{out}
changed = %ne @.status "active"
```

**In**

```odin
status = "closed"
```

**Out**

```odin
{out}
changed = ?true
```

**Notes**

- The exact negation of %eq: same coercion rules apply.
- Use as a condition directly, e.g. %ifElse %ne @.status "active" "flag" "ok".

### `%not` — boolean NOT

Return the logical negation of a boolean value.

**Signature:** `%not <value:boolean> -> boolean`

**Transform**

```odin
{out}
inactive = %not @.active
```

**In**

```odin
active = ?true
```

**Out**

```odin
{out}
inactive = ?false
```

**Notes**

- Single operand; negates its truthiness.
- Often wraps a comparison: %not %eq @.status "closed".

**Avoid**

- `inactive = %not "false"` — the literal string "false" is not one of the recognized truthy tokens (true/yes/y/1), so it is treated as falsy and %not returns ?true — the opposite of intent. Negate an actual boolean path or verb, e.g. %not @.active.

### `%or` — boolean OR

Return true when either condition is truthy.

**Signature:** `%or <a:boolean> <b:boolean> -> boolean`

**Transform**

```odin
{out}
either = %or %eq @.role "admin" %eq @.role "owner"
```

**In**

```odin
role = "owner"
```

**Out**

```odin
{out}
either = ?true
```

**Notes**

- Both operands are %eq verb-expressions producing booleans.
- Exactly two operands; nest %or for additional alternatives.

**Avoid**

- `either = %or "@.role == 'admin'" "@.role == 'owner'"` — the quoted infix expressions are inert literal strings — not truthy — so %or returns ?false and the rule never fires. Build each side from a comparison verb like %eq @.role "admin".

### `%typeOf` — type name of a value

Return the type name of a value as a string.

**Signature:** `%typeOf <value> -> string`

**Transform**

```odin
{out}
kind = %typeOf @.amount
```

**In**

```odin
amount = ##42
```

**Out**

```odin
{out}
kind = "integer"
```

**Notes**

- Returns one of: null, string, integer, number, currency, boolean, date, timestamp, array, object, and more.
- Use it for routing logic when you need the type name rather than a boolean check.

**Avoid**

- `kind = %typeOf @.born` — an ISO date supplied as the bare string "2020-01-15" reports "string", not "date", because it arrives untyped. Coerce with %coerceDate first if you expect a date type.

### `%xor` — exclusive OR

Return true when exactly one of two conditions is truthy.

**Signature:** `%xor <a:boolean> <b:boolean> -> boolean`

**Transform**

```odin
{out}
exactlyOne = %xor @.hasEmail @.hasPhone
```

**In**

```odin
hasEmail = ?true
hasPhone = ?false
```

**Out**

```odin
{out}
exactlyOne = ?true
```

**Notes**

- True when the operands differ; false when both are true or both are false.
- Two operands, both coerced to booleans.

## Lookup

### `%lookup` — map a key through a table

Translate a value via a declared lookup table.

**Signature:** `%lookup <string:"TABLE.column"> <key> [<key>...] -> value`

**Transform**

```odin
{$table.BODY_TYPES[name, code]}
"sedan", "SD"
"coupe", "CP"
"suv", "SU"

{out}
vehicleCode = %lookup "BODY_TYPES.code" @.vehicleType
```

**In**

```odin
vehicleType = "sedan"
```

**Out**

```odin
{out}
vehicleCode = "SD"
```

**Notes**

- The table is declared with a {$table.NAME[col1, col2]} block above the output section.
- The first argument is the quoted "TABLE.column" naming the column to return; remaining arguments are the keys to match.
- A key with no matching row resolves to ~ (null).

**Avoid**

- `vehicleCode = %lookup @.vehicleType "BODY_TYPES.code"` — argument order is "TABLE.column" first then the key; reversing them matches nothing and yields ~
- `vehicleCode = %lookup "code" @.vehicleType` — the column reference must include the table name ("BODY_TYPES.code"); a bare column resolves to ~

### `%lookupDefault` — map a key through a table with a fallback

Translate a value via a declared lookup table, returning a default when no row matches.

**Signature:** `%lookupDefault <string:"TABLE.column"> <key> [<key>...] <default> -> value`

**Transform**

```odin
{$table.BODY_TYPES[name, code]}
"sedan", "SD"
"coupe", "CP"
"suv", "SU"

{out}
vehicleCode = %lookupDefault "BODY_TYPES.code" @.vehicleType "??"
```

**In**

```odin
vehicleType = "wagon"
```

**Out**

```odin
{out}
vehicleCode = "??"
```

**Notes**

- The table is declared with a {$table.NAME[col1, col2]} block above the output section.
- The first argument is the quoted "TABLE.column" naming the column to return; the keys to match follow.
- The last argument is the default value, returned when no row matches (here "wagon" is absent, so "??").
- A matching key returns the cell value instead of the default.

**Avoid**

- `vehicleCode = %lookupDefault "BODY_TYPES.code" @.vehicleType` — the default argument is required; with only the table reference and one key the call has too few arguments and resolves to ~
- `vehicleCode = %lookupDefault @.vehicleType "BODY_TYPES.code" "??"` — the "TABLE.column" reference must come first; reversing it with the key matches nothing and returns the default

## Aggregation

### `%accumulate` — running total across loop records

Add a value to a named accumulator on each loop pass, building a running total.

**Signature:** `%accumulate <name> <number> -> number`

**Transform**

```odin
{$accumulator}
runningTotal = ##0
runningTotal._persist = true

{lines[]}
_loop = "@items"
amount = @.amount
_ = %accumulate runningTotal @.amount

{summary}
total = "@$accumulator.runningTotal"
```

**In**

```odin
{}
{items[] : amount}
##10
##20
##30
```

**Out**

```odin
summary.total = ##60
{lines[] : amount}
##10
##20
##30
```

**Notes**

- The accumulator must be declared in {$accumulator} with _persist = true so it survives across loop records; the first argument names it.
- Each loop pass adds the second argument to the current value; read the result back with @$accumulator.<name>.
- The verb runs as a side effect, conventionally sunk into a throwaway field (_); its return value is the new running total.

**Avoid**

- `_ = %accumulate runningTotal @.amount  (no {$accumulator} block declaring runningTotal)` — an undeclared accumulator makes %accumulate return ~ on every pass, so the total reads as ~ instead of the sum

### `%avg` — arithmetic mean of an array

Reduce an array reference to the mean of its numeric elements.

**Signature:** `%avg <array> -> number`

**Transform**

```odin
{out}
mean = %avg @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
mean = ##20
```

**Notes**

- Sum divided by element count; a whole-number mean is emitted as an integer (##), otherwise as a number (#).
- An empty-but-present array (a single ~ element) averages to ##0; a missing path yields ~.

**Avoid**

- `mean = %avg @.nope  (path does not resolve to an array)` — no array resolves, so %avg returns ~ rather than a zero or an error

### `%count` — number of elements in an array

Return the element count of an array reference as an integer.

**Signature:** `%count <array> -> integer`

**Transform**

```odin
{out}
n = %count @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
n = ##3
```

**Notes**

- Counts elements regardless of their type; the array need not be numeric.
- Reference an object-field array (@.lines) to count records.

**Avoid**

- `n = %count @.x  (where x is a scalar)` — a scalar reference is not an array, so %count silently returns ##0 instead of 1 or an error

### `%first` — first element of an array

Return the first element of an array reference, preserving its type.

**Signature:** `%first <array> -> any`

**Transform**

```odin
{out}
f = %first @.names
```

**In**

```odin
{names[] : ~}
"alice"
"bob"
```

**Out**

```odin
{out}
f = "alice"
```

**Notes**

- Type-preserving: a string element comes back a string, a number stays a number.
- An empty or missing array yields ~ (null).

**Avoid**

- `f = %first @.x  (where x is a scalar)` — %first indexes into an array; a scalar reference resolves to no array and returns ~ instead of the value

### `%last` — last element of an array

Return the last element of an array reference, preserving its type.

**Signature:** `%last <array> -> any`

**Transform**

```odin
{out}
l = %last @.names
```

**In**

```odin
{names[] : ~}
"alice"
"bob"
```

**Out**

```odin
{out}
l = "bob"
```

**Notes**

- Type-preserving: a string element comes back a string, a number stays a number.
- An empty or missing array yields ~ (null).

**Avoid**

- `l = %last @.x  (where x is a scalar)` — %last indexes into an array; a scalar reference resolves to no array and returns ~ instead of the value

### `%max` — largest numeric value in an array

Reduce an array reference to its maximum numeric element.

**Signature:** `%max <array> -> number`

**Transform**

```odin
{out}
hi = %max @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##5
```

**Out**

```odin
{out}
hi = ##20
```

**Notes**

- Folds the whole array; it is not the scalar comparator (that is %maxOf in the numeric family).
- A whole-number maximum is emitted as an integer (##); a fractional one stays a number (#).
- An empty array yields ~ (null).

**Avoid**

- `hi = %max @.x  (where x is a scalar)` — %max reduces an array; a scalar reference resolves to no array and returns ~ instead of the value

### `%min` — smallest numeric value in an array

Reduce an array reference to its minimum numeric element.

**Signature:** `%min <array> -> number`

**Transform**

```odin
{out}
lo = %min @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##5
```

**Out**

```odin
{out}
lo = ##5
```

**Notes**

- Folds the whole array; it is not the scalar comparator (that is %minOf in the numeric family).
- A whole-number minimum is emitted as an integer (##); a fractional one stays a number (#).
- An empty array yields ~ (null).

**Avoid**

- `lo = %min @.x  (where x is a scalar)` — %min reduces an array; a scalar reference resolves to no array and returns ~ instead of the value

### `%set` — overwrite an accumulator with a value

Replace a named accumulator's current value on each loop pass, ending with the last record's value.

**Signature:** `%set <name> <value> -> value`

**Transform**

```odin
{$accumulator}
lastSeen = ##0
lastSeen._persist = true

{lines[]}
_loop = "@items"
val = @.v
_ = %set lastSeen @.v

{summary}
final = "@$accumulator.lastSeen"
```

**In**

```odin
{}
{items[] : v}
##10
##20
##30
```

**Out**

```odin
summary.final = ##30
{lines[] : val}
##10
##20
##30
```

**Notes**

- Unlike %accumulate, %set replaces rather than adds; after a loop the accumulator holds the last record's value.
- Supports any value type (string or number), not just numerics.
- The accumulator must be declared in {$accumulator} with _persist = true; the first argument names it.

**Avoid**

- `_ = %set lastSeen @.v  (no {$accumulator} block declaring lastSeen)` — %set is a no-op on an undeclared accumulator and returns ~, so the read-back yields ~ instead of the value

### `%sum` — total of an array's numeric values

Reduce an array reference to the sum of its numeric elements.

**Signature:** `%sum <array> -> number`

**Transform**

```odin
{out}
total = %sum @.amounts
```

**In**

```odin
{amounts[] : ~}
##10
##20
##30
```

**Out**

```odin
{out}
total = ##60
```

**Notes**

- Takes a single array reference and folds its elements; it is not variadic over scalar arguments.
- A whole-number total is emitted as an integer (##); a fractional total stays a number (#).
- Point the reference at an object-field array (@.lines.amount) to sum one field across records.

**Avoid**

- `total = %sum @.x  (where x is a scalar number)` — %sum expects an array; a scalar reference resolves to no array and silently returns #0 rather than the value or an error

## Financial

### `%clamp` — constrain a value to a range

Bound a value between a minimum and a maximum.

**Signature:** `%clamp <number:value> <number:min> <number:max> -> number`

**Transform**

```odin
{out}
r = %clamp @.value @.min @.max
```

**In**

```odin
value = #150
min = #0
max = #100
```

**Out**

```odin
{out}
r = ##100
```

**Notes**

- A value above max is pulled down to max; a value below min is raised to min.
- Arguments are value, then min, then max, in that order.

**Avoid**

- `r = %clamp @.value @.min` — %clamp requires value, min, and max; fewer than three arguments raises a verb-arity error

### `%compound` — future value of a lump sum

Grow a principal at a per-period rate over n periods: P × (1 + r)^n.

**Signature:** `%compound <number:principal> <number:rate> <number:periods> -> number`

**Transform**

```odin
{out}
r = %compound @.principal @.rate @.years
```

**In**

```odin
principal = #10000
rate = #0.05
years = ##10
```

**Out**

```odin
{out}
r = #16288.946267774423
```

**Notes**

- Arguments are principal, then rate, then periods, in that order.
- The rate is a per-period fraction: 5% is 0.05, not 5.

**Avoid**

- `r = %compound @.principal @.rate @.years  ; rate = #5` — a rate given as 5 (meaning 500%) instead of 0.05 returns ~604661760000 — express the rate as a fraction
- `r = %compound @.principal @.years @.rate` — rate and periods are swapped; the engine computes 10000 × (1+10)^0.05 = ~11273.78, not the intended growth

### `%correlation` — Pearson correlation coefficient

Measure the linear correlation between two numeric series, from −1 to 1.

**Signature:** `%correlation <array:x> <array:y> -> number`

**Transform**

```odin
{out}
r = %correlation @.x @.y
```

**In**

```odin
{x[] : ~}
##1
##2
##3
##4
##5

{y[] : ~}
##2
##4
##5
##4
##6
```

**Out**

```odin
{out}
r = #0.8528028654224417
```

**Notes**

- Result lies in [−1, 1]; positive means the series move together.
- If either series has zero variance the coefficient is undefined and yields ~ (null).

**Avoid**

- `r = %correlation @.x` — %correlation requires two array arguments; one argument raises a verb-arity error

### `%covariance` — population covariance of two series

Measure how two numeric series vary together.

**Signature:** `%covariance <array:x> <array:y> -> number`

**Transform**

```odin
{out}
r = %covariance @.x @.y
```

**In**

```odin
{x[] : ~}
##1
##2
##3
##4
##5

{y[] : ~}
##2
##4
##5
##4
##6
```

**Out**

```odin
{out}
r = #1.6
```

**Notes**

- Uses the population convention (divides by n) over the overlapping length of the two arrays.
- Both arguments must be arrays; if lengths differ the shorter length is used.

### `%depreciation` — straight-line depreciation per period

Compute the constant per-period depreciation: (cost − salvage) / life.

**Signature:** `%depreciation <number:cost> <number:salvage> <number:life> -> number`

**Transform**

```odin
{out}
r = %depreciation @.cost @.salvage @.life
```

**In**

```odin
cost = #10000
salvage = #2000
life = ##5
```

**Out**

```odin
{out}
r = ##1600
```

**Notes**

- An asset costing 10000 with 2000 salvage over a 5-year life depreciates 1600 per year.
- A life <= 0, or a salvage greater than cost, yields ~ (null).

**Avoid**

- `r = %depreciation @.cost @.salvage @.life  ; salvage = #10000, cost = #2000` — when salvage exceeds cost the result is ~ (null), not a negative depreciation

### `%discount` — present value of a future amount

Discount a future value back to today: FV / (1 + r)^n.

**Signature:** `%discount <number:futureValue> <number:rate> <number:periods> -> number`

**Transform**

```odin
{out}
r = %discount @.fv @.rate @.years
```

**In**

```odin
fv = #16288.946267774423
rate = #0.05
years = ##10
```

**Out**

```odin
{out}
r = ##10000
```

**Notes**

- Discounting is the inverse of %compound; the future value of 10000 at 5% for 10 years discounts back to 10000.
- The rate is a per-period fraction: 5% is 0.05, not 5.

**Avoid**

- `r = %discount @.fv @.rate  ; missing periods` — %discount needs futureValue, rate, and periods; fewer than three arguments yields ~ (null)

### `%exp` — exponential (e^x)

Raise Euler's number e to the given power.

**Signature:** `%exp <number:value> -> number`

**Transform**

```odin
{out}
r = %exp @.x
```

**In**

```odin
x = #1
```

**Out**

```odin
{out}
r = #2.718281828459045
```

**Notes**

- exp(1) is e, the base of the natural logarithm.
- The full-precision float is preserved in the output.

### `%fv` — future value of an annuity

Accumulate a stream of equal payments at a per-period rate: PMT × ((1+r)^n − 1) / r.

**Signature:** `%fv <number:payment> <number:rate> <number:periods> -> number`

**Transform**

```odin
{out}
r = %fv @.payment @.rate @.periods
```

**In**

```odin
payment = #500
rate = #0.005
periods = ##120
```

**Out**

```odin
{out}
r = #81939.67340322901
```

**Notes**

- Saving 500 per period at 0.5% per period for 120 periods accumulates to about 81939.67.
- First argument is the periodic payment, not a present lump sum (use %compound for a lump sum).
- A zero rate falls back to payment × periods.

### `%interpolate` — linear interpolation between two points

Estimate y at x along the line through (x1, y1) and (x2, y2).

**Signature:** `%interpolate <number:x> <number:x1> <number:y1> <number:x2> <number:y2> -> number`

**Transform**

```odin
{out}
r = %interpolate @.x @.x1 @.y1 @.x2 @.y2
```

**In**

```odin
x = #5
x1 = #0
y1 = #100
x2 = #10
y2 = #200
```

**Out**

```odin
{out}
r = ##150
```

**Notes**

- x = 5 is halfway between x1 = 0 and x2 = 10, so y is halfway between 100 and 200.
- Argument order is x, x1, y1, x2, y2; when x2 equals x1 the result is y1.

**Avoid**

- `r = %interpolate @.x @.x1 @.y1 @.x2` — %interpolate requires all five arguments; fewer raises a verb-arity error

### `%irr` — internal rate of return

Find the discount rate that makes the net present value of a cash-flow series zero.

**Signature:** `%irr <array:cashflows> <number:guess?> -> number`

**Transform**

```odin
{out}
r = %irr @.cashflows
```

**In**

```odin
{cashflows[] : ~}
#-100
#30
#40
#50
#60
```

**Out**

```odin
{out}
r = #0.24888335658556993
```

**Notes**

- Solved by Newton–Raphson from a default 10% guess; the result is the per-period rate as a fraction.
- Needs at least two cash flows; an optional second argument overrides the initial guess.
- Series with no sign change, or that fail to converge, yield ~ (null).

### `%ln` — natural logarithm

Take the natural logarithm (base e) of a value.

**Signature:** `%ln <number:value> -> number`

**Transform**

```odin
{out}
r = %ln @.x
```

**In**

```odin
x = #2.718281828459045
```

**Out**

```odin
{out}
r = ##1
```

**Notes**

- ln(e) is 1; passing Euler's number returns the integer 1.
- A value <= 0 yields ~ (null).

### `%log` — logarithm with optional base

Take the logarithm of a value, natural by default or in a given base.

**Signature:** `%log <number:value> <number:base?> -> number`

**Transform**

```odin
{out}
r = %log @.x @.b
```

**In**

```odin
x = #8
b = #2
```

**Out**

```odin
{out}
r = ##3
```

**Notes**

- With a second argument the result is log(value) / log(base); here log base 2 of 8 is 3.
- Omit the base for the natural logarithm.
- A value <= 0, or a base <= 0 or equal to 1, yields ~ (null).

### `%log10` — base-10 logarithm

Take the base-10 logarithm of a value.

**Signature:** `%log10 <number:value> -> number`

**Transform**

```odin
{out}
r = %log10 @.x
```

**In**

```odin
x = #1000
```

**Out**

```odin
{out}
r = ##3
```

**Notes**

- log10(1000) is 3.
- A value <= 0 yields ~ (null).

### `%median` — middle value

Return the median of a numeric array, averaging the two middle values when the count is even.

**Signature:** `%median <array:values> -> number`

**Transform**

```odin
{out}
r = %median @.prices
```

**In**

```odin
{prices[] : ~}
#19.5
#21.0
#20.25
#22.75
#18.0
```

**Out**

```odin
{out}
r = #20.25
```

**Notes**

- The array is sorted internally; input order does not matter.
- For an even number of elements the result is the mean of the two central values.

### `%mode` — most frequent value

Return the most frequently occurring value in a numeric array.

**Signature:** `%mode <array:values> -> number`

**Transform**

```odin
{out}
r = %mode @.values
```

**In**

```odin
{values[] : ~}
##3
##5
##3
##7
##3
```

**Out**

```odin
{out}
r = ##3
```

**Notes**

- 3 appears three times, more than any other value.
- On a tie the first value to reach the highest count wins.

### `%movingAvg` — rolling window average

Smooth a numeric series with a simple moving average over a fixed window.

**Signature:** `%movingAvg <array:values> <integer:window> -> array`

**Transform**

```odin
{out}
r = %movingAvg @.prices @.w
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
##40
##50

w = ##3
```

**Out**

```odin
{out}
{.r[] : ~}
##10
##15
##20
##30
##40
```

**Notes**

- Returns an array the same length as the input; leading positions use a partial window of the data available so far.
- The result is emitted as an array block ({.r[] : ~}) since each position has its own average.
- A window < 1 yields ~ (null).

**Avoid**

- `r = %movingAvg @.prices @.w  ; w = ##0` — a window size below 1 yields ~ (null); the window must be a positive integer

### `%nper` — number of periods

Solve for how many periods are needed given a rate, payment, present value, and future value.

**Signature:** `%nper <number:rate> <number:pmt> <number:pv> <number:fv> -> number`

**Transform**

```odin
{out}
r = %nper @.rate @.pmt @.pv @.fv
```

**In**

```odin
rate = #0.05
pmt = #-1000
pv = #10000
fv = #0
```

**Out**

```odin
{out}
r = #14.206699082890461
```

**Notes**

- Closed-form: log((pmt − rate·fv) / (pmt + rate·pv)) / log(1 + rate).
- Sign convention: the present value (pv) is positive and the payments are negative.
- Argument order is rate, pmt, pv, fv; a zero rate falls back to −(pv + fv) / pmt.

### `%npv` — net present value of cash flows

Discount a series of period cash flows at a rate and sum them, with the first flow at t=0.

**Signature:** `%npv <number:rate> <array:cashflows> -> number`

**Transform**

```odin
{out}
r = %npv @.rate @.cashflows
```

**In**

```odin
rate = #0.1

{cashflows[] : ~}
#-100
#30
#40
#50
#60
```

**Out**

```odin
{out}
r = #38.87712587937978
```

**Notes**

- The first cash flow is taken at period 0 (undiscounted); subsequent flows are discounted at (1+rate)^t.
- Argument order is rate first, then the cash-flow array.

**Avoid**

- `r = %npv @.cashflows @.rate` — passing the array where the scalar rate is expected yields ~ (null); the rate must be the first argument

### `%percentile` — Nth percentile (0–100)

Return the value at a given percentile of a numeric array using linear interpolation.

**Signature:** `%percentile <array:values> <number:pct> -> number`

**Transform**

```odin
{out}
r = %percentile @.values @.p
```

**In**

```odin
{values[] : ~}
##10
##20
##30
##40
##50

p = ##90
```

**Out**

```odin
{out}
r = ##46
```

**Notes**

- The percentile is expressed on a 0–100 scale; 90 means the 90th percentile.
- When the rank falls between two points the result is linearly interpolated.

**Avoid**

- `r = %percentile @.values @.p  ; p = #0.9` — %percentile expects 0–100, so 0.9 is read as the 0.9th percentile and returns a value near the minimum, not the 90th percentile; use %quantile for a 0–1 scale

### `%pmt` — level payment for a loan

Compute the constant per-period payment that amortizes a principal over n periods.

**Signature:** `%pmt <number:principal> <number:rate> <number:periods> -> number`

**Transform**

```odin
{out}
r = %pmt @.principal @.rate @.periods
```

**In**

```odin
principal = #200000
rate = #0.005
periods = ##360
```

**Out**

```odin
{out}
r = #1199.1010503055138
```

**Notes**

- A 200,000 loan at 0.5% per month over 360 months has a monthly payment near 1199.10.
- The rate is the per-period rate; for an annual rate divide by the number of periods per year.
- A zero rate falls back to principal / periods; periods <= 0 yields ~ (null).

**Avoid**

- `r = %pmt @.principal @.annualRate @.periods  ; annualRate = #0.06, periods = ##360` — mixing an annual rate with a monthly period count overstates the payment; convert the rate to per-period (0.06/12 = 0.005)

### `%pow` — raise base to an exponent

Raise a base to an exponent (e.g. a growth factor over n periods).

**Signature:** `%pow <number:base> <number:exponent> -> number`

**Transform**

```odin
{out}
r = %pow @.base @.exp
```

**In**

```odin
base = #1.05
exp = #10
```

**Out**

```odin
{out}
r = #1.6288946267774422
```

**Notes**

- 1.05^10 is the ten-period growth factor for a 5% rate.
- A non-finite result (e.g. overflow) yields ~ (null).

**Avoid**

- `r = %pow @.base` — %pow needs both base and exponent; one argument yields ~ (null)

### `%pv` — present value of an annuity

Discount a stream of equal payments to today: PMT × (1 − (1+r)^-n) / r.

**Signature:** `%pv <number:payment> <number:rate> <number:periods> -> number`

**Transform**

```odin
{out}
r = %pv @.payment @.rate @.periods
```

**In**

```odin
payment = #1000
rate = #0.005
periods = ##120
```

**Out**

```odin
{out}
r = #90073.45332716724
```

**Notes**

- Receiving 1000 per period at 0.5% per period for 120 periods is worth about 90073.45 today.
- First argument is the periodic payment; use %discount for a single future amount.
- A zero rate falls back to payment × periods.

### `%quantile` — quantile (0–1)

Return the value at a given quantile of a numeric array using linear interpolation.

**Signature:** `%quantile <array:values> <number:q> -> number`

**Transform**

```odin
{out}
r = %quantile @.values @.q
```

**In**

```odin
{values[] : ~}
##10
##20
##30
##40
##50

q = #0.25
```

**Out**

```odin
{out}
r = ##20
```

**Notes**

- The quantile is expressed on a 0–1 scale; 0.25 is the first quartile.
- Same interpolation as %percentile but with a 0–1 argument instead of 0–100.

**Avoid**

- `r = %quantile @.values @.q  ; q = ##25` — %quantile expects 0–1, so 25 is out of range and yields ~ (null); use %percentile for a 0–100 scale

### `%rate` — interest rate per period

Solve for the per-period rate given the number of periods, payment, present value, and future value.

**Signature:** `%rate <number:nper> <number:pmt> <number:pv> <number:fv> -> number`

**Transform**

```odin
{out}
r = %rate @.nper @.pmt @.pv @.fv
```

**In**

```odin
nper = ##12
pmt = #-1000
pv = #10000
fv = #0
```

**Out**

```odin
{out}
r = #0.02922854076913369
```

**Notes**

- Solved by Newton–Raphson; the result is the per-period rate as a fraction (about 2.92% here).
- Sign convention: an inflow (pv) is positive and the offsetting payments are negative.
- Argument order is nper, pmt, pv, fv.

**Avoid**

- `r = %rate @.nper @.pmt @.pv  ; missing fv` — %rate requires all four arguments; fewer than four yields ~ (null)

### `%sqrt` — square root

Take the square root of a non-negative value.

**Signature:** `%sqrt <number:value> -> number`

**Transform**

```odin
{out}
r = %sqrt @.x
```

**In**

```odin
x = #2
```

**Out**

```odin
{out}
r = #1.4142135623730951
```

**Notes**

- The full-precision float is preserved in the output.
- A negative value yields ~ (null).

**Avoid**

- `r = %sqrt @.x  ; x = #-4` — a negative radicand yields ~ (null), not a complex or NaN result

### `%std` — population standard deviation

Compute the population standard deviation of a numeric array.

**Signature:** `%std <array:values> -> number`

**Transform**

```odin
{out}
r = %std @.returns
```

**In**

```odin
{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01
```

**Out**

```odin
{out}
r = #0.02
```

**Notes**

- Divides the summed squared deviations by n (population), not n−1.
- Provide the array as a typed array block ({returns[] : ~}); a scalar argument yields ~ (null).

**Avoid**

- `r = %std @.x  ; x = #5` — passing a scalar instead of an array yields ~ (null); %std needs a numeric array

### `%stdSample` — sample standard deviation

Compute the sample standard deviation (n−1 denominator) of a numeric array.

**Signature:** `%stdSample <array:values> -> number`

**Transform**

```odin
{out}
r = %stdSample @.returns
```

**In**

```odin
{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01
```

**Out**

```odin
{out}
r = #0.0223606797749979
```

**Notes**

- Divides the summed squared deviations by n−1 (Bessel's correction), so it exceeds %std on the same data.
- Requires at least two elements; a shorter array yields ~ (null).

### `%variance` — population variance

Compute the population variance of a numeric array.

**Signature:** `%variance <array:values> -> number`

**Transform**

```odin
{out}
r = %variance @.returns
```

**In**

```odin
{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01
```

**Out**

```odin
{out}
r = #0.0004000000000000001
```

**Notes**

- Divides the summed squared deviations by n (population); it is the square of %std.
- The full-precision float, including floating-point noise, is preserved in the output.

### `%varianceSample` — sample variance

Compute the sample variance (n−1 denominator) of a numeric array.

**Signature:** `%varianceSample <array:values> -> number`

**Transform**

```odin
{out}
r = %varianceSample @.returns
```

**In**

```odin
{returns[] : ~}
#0.02
#0.05
#-0.01
#0.03
#0.01
```

**Out**

```odin
{out}
r = #0.0005000000000000001
```

**Notes**

- Divides the summed squared deviations by n−1 (Bessel's correction); it is the square of %stdSample.
- Requires at least two elements; a shorter array yields ~ (null).

### `%weightedAvg` — weighted average of two arrays

Average values weighted by a parallel array of weights.

**Signature:** `%weightedAvg <array:values> <array:weights> -> number`

**Transform**

```odin
{out}
r = %weightedAvg @.values @.weights
```

**In**

```odin
{values[] : ~}
#90
#80
#70

{weights[] : ~}
#3
#2
#1
```

**Out**

```odin
{out}
r = #83.33333333333333
```

**Notes**

- Computes sum(value × weight) / sum(weight); here (90·3 + 80·2 + 70·1) / 6.
- First argument is the values array, second is the weights array — order matters.

**Avoid**

- `r = %weightedAvg @.weights @.values` — swapping values and weights changes the result (here to ~2.083); the values array must come first

### `%zscore` — standard score of a value

Express how many population standard deviations a value lies from a dataset's mean.

**Signature:** `%zscore <number:value> <array:dataset> -> number`

**Transform**

```odin
{out}
r = %zscore @.value @.scores
```

**In**

```odin
value = ##85

{scores[] : ~}
##70
##75
##80
##85
##90
```

**Out**

```odin
{out}
r = #0.7071067811865475
```

**Notes**

- Uses the population standard deviation of the dataset; here mean 80, std ~7.07, so 85 is ~0.71 std above.
- Argument order is the value first, then the dataset array.
- If every dataset value is identical the standard deviation is 0 and the result is ~ (null).

## Type Coercion

### `%coerceBoolean` — convert a value to a boolean

Coerce truthy/falsy text or numbers to a typed boolean.

**Signature:** `%coerceBoolean <value> -> boolean`

**Transform**

```odin
{out}
b = %coerceBoolean @.raw
```

**In**

```odin
raw = "yes"
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Truthy strings are "true", "yes", "y", and "1" (case-insensitive); everything else is ?false.
- Numbers coerce by zero/non-zero, so 0 is ?false and any other number is ?true.

**Avoid**

- `b = %coerceBoolean @.raw  ; input "maybe"` — any string outside the truthy set silently becomes ?false, not an error

### `%coerceDate` — convert a value to a date

Parse a string (or number/timestamp) into a typed date, dropping any time component.

**Signature:** `%coerceDate <value> -> date`

**Transform**

```odin
{out}
d = %coerceDate @.raw
```

**In**

```odin
raw = "2024-06-15"
```

**Out**

```odin
{out}
d = 2024-06-15
```

**Notes**

- The quoted string becomes a bare date value (no quotes, no prefix) rendered as YYYY-MM-DD.
- Many input shapes parse: ISO, YYYYMMDD, MM/DD/YYYY, DD.MM.YYYY, written months, and numeric Unix timestamps.
- An unparseable value yields ~ (null) instead of an error.

**Avoid**

- `d = %coerceDate @.raw  ; input "not-a-date"` — an unrecognized date string silently becomes ~ (null), never an error

### `%coerceInteger` — parse a value to an integer

Convert a string (or number) to a typed integer.

**Signature:** `%coerceInteger <value> -> integer`

**Transform**

```odin
{out}
count = %coerceInteger @.countText
```

**In**

```odin
countText = "42"
```

**Out**

```odin
{out}
count = ##42
```

**Notes**

- The output is a true integer (##), so downstream numeric verbs treat it as a number.
- A non-numeric input coerces to ##0 rather than raising an error — validate upstream if that matters.

**Avoid**

- `count = %coerceInteger @.countText  ; input "not-a-number"` — unparseable input silently becomes ##0; there is no error to catch

### `%coerceNumber` — parse a value to a number

Convert a string (or other value) to a typed number, keeping the fractional part.

**Signature:** `%coerceNumber <value> -> number`

**Transform**

```odin
{out}
n = %coerceNumber @.raw
```

**In**

```odin
raw = "3.14"
```

**Out**

```odin
{out}
n = #3.14
```

**Notes**

- The quoted string "3.14" becomes the typed number #3.14.
- A whole-number input like "42" emits as ##42 (integer prefix) because the value has no fractional part — coerceNumber preserves fractions but collapses whole values to integers.
- A non-numeric input coerces to ##0 rather than raising an error — validate upstream if that matters.

**Avoid**

- `n = %coerceNumber @.raw  ; input "3.9" expecting ##3` — coerceNumber keeps the fraction (#3.9); use %coerceInteger to floor to ##3

### `%coerceString` — convert any value to a string

Force a value to a quoted string regardless of its source type.

**Signature:** `%coerceString <value> -> string`

**Transform**

```odin
{out}
s = %coerceString @.raw
```

**In**

```odin
raw = ##42
```

**Out**

```odin
{out}
s = "42"
```

**Notes**

- The typed integer ##42 loses its # prefix and becomes the quoted string "42".
- A null input coerces to the empty string "" rather than passing ~ through.

### `%coerceTimestamp` — convert a value to a timestamp

Parse a value into a typed timestamp, preserving the time-of-day component.

**Signature:** `%coerceTimestamp <value> -> timestamp`

**Transform**

```odin
{out}
t = %coerceTimestamp @.raw
```

**In**

```odin
raw = "2024-06-15T09:30:00Z"
```

**Out**

```odin
{out}
t = 2024-06-15T09:30:00.000Z
```

**Notes**

- Unlike %coerceDate, the time component survives and the value renders as a full ISO 8601 timestamp.
- The output is normalized to UTC with millisecond precision (.000Z).
- An unparseable value yields ~ (null).

**Avoid**

- `t = %coerceDate @.raw  ; when you need the time-of-day` — coerceDate discards the time and renders only YYYY-MM-DD; use coerceTimestamp to keep HH:MM:SS

### `%toArray` — wrap a value in an array

Normalize a scalar (or existing array) into an array so downstream array verbs always have a collection.

**Signature:** `%toArray <value> -> array`

**Transform**

```odin
{out}
arr = %toArray @.raw
```

**In**

```odin
raw = "single"
```

**Out**

```odin
{out.arr[] : ~}
"single"
```

**Notes**

- A scalar becomes a single-element array, rendered as an array sub-block headed by {out.arr[] : ~}.
- A value that is already an array passes through unchanged.
- A null input becomes an empty array, which the serializer elides entirely (no output line).

**Avoid**

- `arr = %toArray @.raw  ; input ~ then expecting an empty [] line` — a null coerces to an empty array that the serializer omits, so no array block appears at all

### `%toObject` — build an object from key/value pairs

Fold an array of [key, value] pairs into a single object keyed by the first element.

**Signature:** `%toObject <array> -> object`

**Transform**

```odin
{out}
obj = %toObject @.pairs
```

**In**

```odin
pairs[0][0] = "a"
pairs[0][1] = ##1
pairs[1][0] = "b"
pairs[1][1] = ##2
```

**Out**

```odin
{out}
{.obj}
a = ##1
b = ##2
```

**Notes**

- Each [key, value] pair becomes a field, rendered as the {.obj} sub-block.
- Pairs may also be {key, value} or {k, v} objects; the key is stringified.
- A non-array input yields ~ (null).

**Avoid**

- `obj = %toObject @.raw  ; input is a plain string` — toObject only accepts arrays; any non-array input silently produces ~ (null)

### `%tryCoerce` — auto-detect and coerce a string to its natural type

Infer the type of a string value (integer, number, boolean, date) and coerce it, leaving plain text unchanged.

**Signature:** `%tryCoerce <value> -> value`

**Transform**

```odin
{out}
v = %tryCoerce @.raw
```

**In**

```odin
raw = "42"
```

**Out**

```odin
{out}
v = ##42
```

**Notes**

- Detection order is null/empty, boolean, integer, number, ISO date, ISO timestamp; the first match wins.
- "3.14" detects as #3.14, "true" as ?true, and a plain word like "John" passes through as the string "John".
- Designed for XML/CSV ingests where every field arrives as a string; non-string inputs are returned unchanged.

**Avoid**

- `v = %tryCoerce @.raw  ; input "John" expecting a number` — values that match no type pattern are returned as the original string, not coerced or nulled

## Encoding

### `%base64Decode` — Decode a Base64 string

Decode Base64 text back into its original UTF-8 string.

**Signature:** `%base64Decode <string> -> string`

**Transform**

```odin
{out}
dec = %base64Decode @.b
```

**In**

```odin
b = "SGVsbG8sIFdvcmxkIQ=="
```

**Out**

```odin
{out}
dec = "Hello, World!"
```

**Notes**

- Standard and URL-safe alphabets are both accepted (+/ or -_).
- Input that is not valid Base64 decodes to ~ (null) rather than raising an error.

**Avoid**

- `dec = %base64Decode @.b   ; with b = "Hello!"` — a value that is not valid Base64 decodes to ~, not the original text

### `%base64Encode` — Base64-encode a string

Encode a UTF-8 string as Base64 text.

**Signature:** `%base64Encode <string> -> string`

**Transform**

```odin
{out}
encoded = %base64Encode @.content
```

**In**

```odin
content = "Hello, World!"
```

**Out**

```odin
{out}
encoded = "SGVsbG8sIFdvcmxkIQ=="
```

**Notes**

- The input is treated as UTF-8 before encoding, so non-ASCII text encodes its byte sequence.
- The result is a plain string, not an ODIN binary (^) value.

**Avoid**

- `encoded = %base64Encode content` — a bare word encodes the literal text "content", not the field; use @.content

### `%crc32` — CRC-32 checksum of a string

Compute the CRC-32 (IEEE) checksum of a string as lowercase hex.

**Signature:** `%crc32 <string> -> string`

**Transform**

```odin
{out}
h = %crc32 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "3610a686"
```

**Notes**

- The checksum is deterministic and 8 lowercase hex characters long.
- CRC-32 is for data-integrity checks only, not cryptographic use.

### `%hexDecode` — Decode a hexadecimal string

Decode a hexadecimal string back into its original text.

**Signature:** `%hexDecode <string> -> string`

**Transform**

```odin
{out}
d = %hexDecode @.h
```

**In**

```odin
h = "48656C6C6F"
```

**Out**

```odin
{out}
d = "Hello"
```

**Notes**

- Upper- and lower-case hex digits are both accepted.
- An odd-length or non-hex string decodes to ~ (null) rather than raising an error.

**Avoid**

- `d = %hexDecode @.h   ; with h = "48656"` — an odd number of hex digits is invalid and yields ~

### `%hexEncode` — Encode a string as hexadecimal

Encode a string as a lowercase hexadecimal byte sequence.

**Signature:** `%hexEncode <string> -> string`

**Transform**

```odin
{out}
h = %hexEncode @.data
```

**In**

```odin
data = "Hello"
```

**Out**

```odin
{out}
h = "48656c6c6f"
```

**Notes**

- Each byte is rendered as two lowercase hex digits.
- Characters above U+007F are encoded as their UTF-8 byte sequence.

### `%jsonDecode` — Parse JSON text into a value

Parse a JSON object or array string into a structured ODIN value.

**Signature:** `%jsonDecode <string> -> value`

**Transform**

```odin
{out}
d = %jsonDecode @.j
```

**In**

```odin
j = "{\"name\":\"John\",\"age\":42}"
```

**Out**

```odin
{out}
{.d}
name = "John"
age = ##42
```

**Notes**

- A decoded object becomes a nested block, so the target field d renders as the {.d} subsection.
- Strings that are not JSON objects/arrays are treated as escaped text and unescaped in place.

### `%jsonEncode` — Serialize a value to JSON text

Serialize an object or array into a compact JSON string.

**Signature:** `%jsonEncode <value> -> string`

**Transform**

```odin
{out}
j = %jsonEncode @.data
```

**In**

```odin
data.name = "John"
data.age = ##42
```

**Out**

```odin
{out}
j = "{\"name\":\"John\",\"age\":42}"
```

**Notes**

- Objects and arrays produce their full JSON serialization as a single string.
- For a scalar string, the result is the JSON-escaped body without the surrounding quotes.

### `%jsonPath` — Query a value with a JSONPath expression

Extract a nested value from an object using a JSONPath-style expression.

**Signature:** `%jsonPath <value> <path:string> -> value`

**Transform**

```odin
{out}
name = %jsonPath @.data "$.user.name"
```

**In**

```odin
data.user.name = "Ada"
data.user.id = ##7
```

**Out**

```odin
{out}
name = "Ada"
```

**Notes**

- Supports dot notation, [n] array indices, [*] wildcards, and ..field recursive descent.
- The path is a required second argument; a missing path yields ~ (null).

**Avoid**

- `name = %jsonPath @.data` — the path argument is required; without it the verb yields ~

### `%md5` — MD5 hash of a string

Compute the MD5 digest of a string as lowercase hex.

**Signature:** `%md5 <string> -> string`

**Transform**

```odin
{out}
h = %md5 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "5d41402abc4b2a76b9719d911017c592"
```

**Notes**

- The digest is deterministic and 32 lowercase hex characters long.
- MD5 is suitable for checksums and cache keys only, not for security.

### `%sha1` — SHA-1 hash of a string

Compute the SHA-1 digest of a string as lowercase hex.

**Signature:** `%sha1 <string> -> string`

**Transform**

```odin
{out}
h = %sha1 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d"
```

**Notes**

- The digest is deterministic and 40 lowercase hex characters long.
- SHA-1 is acceptable for content addressing but is deprecated for security.

### `%sha256` — SHA-256 hash of a string

Compute the SHA-256 digest of a string as lowercase hex.

**Signature:** `%sha256 <string> -> string`

**Transform**

```odin
{out}
h = %sha256 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824"
```

**Notes**

- The digest is deterministic: the same input always yields the same 64-character lowercase hex string.
- The input is hashed as UTF-8 bytes.

### `%sha512` — SHA-512 hash of a string

Compute the SHA-512 digest of a string as lowercase hex.

**Signature:** `%sha512 <string> -> string`

**Transform**

```odin
{out}
h = %sha512 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "9b71d224bd62f3785d96d46ad3ea3d73319bfbc2890caadae2dff72519673ca72323c3d99ba5c11d7c7acc6e14b8c5da0c4663475c2e5c3adef46f73bcdec043"
```

**Notes**

- The digest is deterministic and 128 lowercase hex characters long.
- SHA-512 produces a 512-bit output, wider than SHA-256.

### `%urlDecode` — Decode a percent-encoded string

Reverse percent-encoding, turning a URL component back into plain text.

**Signature:** `%urlDecode <string> -> string`

**Transform**

```odin
{out}
dec = %urlDecode @.q
```

**In**

```odin
q = "a%20b%26c%3Dd"
```

**Out**

```odin
{out}
dec = "a b&c=d"
```

**Notes**

- Inverse of %urlEncode: %20 becomes a space, %26 an ampersand, and so on.
- A malformed escape (such as %zz) decodes to ~ (null) rather than raising an error.

**Avoid**

- `dec = %urlDecode @.q   ; with q = "%zz"` — an invalid percent escape yields ~, not partial output

### `%urlEncode` — Percent-encode a string

Percent-encode a string so it is safe inside a URL component.

**Signature:** `%urlEncode <string> -> string`

**Transform**

```odin
{out}
enc = %urlEncode @.q
```

**In**

```odin
q = "a b&c=d"
```

**Out**

```odin
{out}
enc = "a%20b%26c%3Dd"
```

**Notes**

- Encodes a single URI component: spaces become %20 and reserved characters such as & and = are escaped.
- Unreserved characters (letters, digits, - _ . ! ~ * ' ( )) pass through unchanged.

## Generation

### `%resetSequence` — reset a named counter

Reset a named counter so a later %sequence on that name restarts.

**Signature:** `%resetSequence <string:name> -> integer`

**Transform**

```odin
{out}
resetTo = %resetSequence "ctr"
```

**In**

```odin
x = "a"
```

**Out**

```odin
{out}
resetTo = ##0
```

**Notes**

- The single argument is the quoted counter name; resetting returns ##0, and the next %sequence on that name yields ##1.
- Counter state is scoped to one transform execution.
- Deterministic, so it is corpus-safe.

**Avoid**

- `r = %resetSequence "ctr" ##5` — an inline reset value is rejected — a literal after the name raises a parse error (Unexpected content after value); the reset is always to 0

### `%sequence` — next value in a named counter

Emit successive integers from a named counter that persists across mappings in one execution.

**Signature:** `%sequence <string:name> -> integer`

**Transform**

```odin
{out}
first = %sequence "ln"
second = %sequence "ln"
third = %sequence "ln"
```

**In**

```odin
x = "a"
```

**Out**

```odin
{out}
first = ##1
second = ##2
third = ##3
```

**Notes**

- The single argument is the quoted counter name; the first reference yields ##1 and each later reference to the same name increments.
- Counter state is scoped to one transform execution.
- Deterministic, so it is corpus-safe (unlike %uuid or %nanoid).

**Avoid**

- `n = %sequence "ln" ##100` — an inline start value is rejected — a literal after the name raises a parse error (Unexpected content after value); %sequence takes only the name and always begins at 1

## Geo

### `%bearing` — initial heading between two points

Compute the initial bearing (forward azimuth) in degrees from one point to another.

**Signature:** `%bearing <number:lat1> <number:lng1> <number:lat2> <number:lng2> -> number`

**Transform**

```odin
{out}
heading = %bearing @.lat1 @.lng1 @.lat2 @.lng2
```

**In**

```odin
lat1 = #40.7128
lng1 = #-74.006
lat2 = #34.0522
lng2 = #-118.2437
```

**Out**

```odin
{out}
heading = #273.6871323393308
```

**Notes**

- Arguments are lat1, lng1, lat2, lng2; the result is degrees normalized to 0-360 (0 = North, 90 = East).
- Coordinates outside lat +/-90 or lng +/-180 resolve to ~.

### `%distance` — great-circle distance between two points

Compute the Haversine distance between two lat/lng coordinates in kilometers.

**Signature:** `%distance <number:lat1> <number:lng1> <number:lat2> <number:lng2> [<string:unit>] -> number`

**Transform**

```odin
{out}
km = %distance @.lat1 @.lng1 @.lat2 @.lng2
```

**In**

```odin
lat1 = #40.7128
lng1 = #-74.006
lat2 = #34.0522
lng2 = #-118.2437
```

**Out**

```odin
{out}
km = #3935.746254609722
```

**Notes**

- Arguments are lat1, lng1, lat2, lng2; the result is a number (#) in kilometers by default.
- Pass a fifth "miles" or "mi" argument for miles.
- Coordinates outside lat +/-90 or lng +/-180 resolve to ~.

### `%inBoundingBox` — test a point against a rectangle

Return true when a lat/lng point falls inside a min/max bounding box.

**Signature:** `%inBoundingBox <number:lat> <number:lng> <number:minLat> <number:minLng> <number:maxLat> <number:maxLng> -> boolean`

**Transform**

```odin
{out}
inside = %inBoundingBox @.lat @.lng #24.5 #-125 #49.5 #-66
```

**In**

```odin
lat = #40.7128
lng = #-74.006
```

**Out**

```odin
{out}
inside = ?true
```

**Notes**

- Argument order is lat, lng, minLat, minLng, maxLat, maxLng; the result is a boolean (?).
- Bounds are inclusive on every edge.

**Avoid**

- `inside = %inBoundingBox @.lat @.lng ##24.5 #-125 #49.5 #-66` — fractional bounds must use the number prefix (#); ##24.5 raises a parse error because an integer (##) cannot have a fractional part

### `%midpoint` — geographic midpoint of two points

Compute the great-circle midpoint between two lat/lng coordinates.

**Signature:** `%midpoint <number:lat1> <number:lng1> <number:lat2> <number:lng2> -> object`

**Transform**

```odin
{out}
mid = %midpoint @.lat1 @.lng1 @.lat2 @.lng2
```

**In**

```odin
lat1 = #40.7128
lng1 = #-74.006
lat2 = #34.0522
lng2 = #-118.2437
```

**Out**

```odin
{out}
{.mid}
lat = #39.510307565757
lon = #-97.16013188872247
```

**Notes**

- Arguments are lat1, lng1, lat2, lng2; the result is an object with lat and lon members, serialized as a {.mid} sub-section.
- The object keys are lat and lon (not lng).

### `%toDegrees` — radians to degrees

Convert an angle in radians to degrees.

**Signature:** `%toDegrees <number:radians> -> number`

**Transform**

```odin
{out}
deg = %toDegrees @.rad
```

**In**

```odin
rad = #3.141592653589793
```

**Out**

```odin
{out}
deg = #180
```

**Notes**

- Single numeric argument in radians; pi radians maps to 180 degrees.
- The result is a number (#).

### `%toRadians` — degrees to radians

Convert an angle in degrees to radians.

**Signature:** `%toRadians <number:degrees> -> number`

**Transform**

```odin
{out}
rad = %toRadians @.deg
```

**In**

```odin
deg = #180
```

**Out**

```odin
{out}
rad = #3.141592653589793
```

**Notes**

- Single numeric argument in degrees; 180 degrees maps to pi radians.
- The result is a number (#).

## Object

### `%entries` — key/value pairs of an object

Return an object as an array of [key, value] pairs.

**Signature:** `%entries <object> -> array`

**Transform**

```odin
{out}
e = %entries @.obj
```

**In**

```odin
{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.e[] : [0], [1]}
"name", "Ada"
"role", "admin"
"active", ?true
```

**Notes**

- The single argument is an object; the result is an array of two-element pairs serialized as a {.e[] : [0], [1]} table.
- Each row is key then value, preserving the value's type and source key order.
- A non-object argument resolves to ~.

### `%get` — read a property with an optional default

Read a value at a key path from an object, falling back to a default when absent.

**Signature:** `%get <object> <string:path> [<default>] -> value`

**Transform**

```odin
{out}
g = %get @.obj "role"
```

**In**

```odin
{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
g = "admin"
```

**Notes**

- The second argument is a quoted key or dot path ("a.b.c"); the resolved value keeps its type.
- An optional third argument is returned when the path is missing or references an unsafe property; with no default a miss yields ~.
- A non-object first argument returns the default (or ~).

### `%has` — test for a property

Return true when an object contains a key, with dot notation for nested paths.

**Signature:** `%has <object> <string:key> -> boolean`

**Transform**

```odin
{out}
h = %has @.obj "role"
```

**In**

```odin
{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
h = ?true
```

**Notes**

- The second argument is a quoted key; the result is a boolean (?).
- Dot notation walks nested objects ("a.b.c"); missing keys and prototype-pollution keys yield ?false.
- A non-object first argument yields ?false.

### `%keys` — property names of an object

Return the own enumerable property names of an object as an array.

**Signature:** `%keys <object> -> array`

**Transform**

```odin
{out}
k = %keys @.obj
```

**In**

```odin
{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.k[] : ~}
"name"
"role"
"active"
```

**Notes**

- The single argument is an object; the result is an array of key strings serialized as a {.k[] : ~} block.
- Keys preserve source order; prototype-pollution keys (__proto__, constructor, prototype) are excluded.
- A non-object argument resolves to ~.

### `%merge` — shallow merge of two objects

Combine two objects, with the second object's properties overriding the first.

**Signature:** `%merge <object> <object> -> object`

**Transform**

```odin
{out}
m = %merge @.a @.b
```

**In**

```odin
{a}
name = "Ada"
role = "user"

{b}
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.m}
name = "Ada"
role = "admin"
active = ?true
```

**Notes**

- Both arguments are objects; the result is a new object serialized as a {.m} sub-section.
- Keys in the second object override matching keys in the first (role becomes "admin"); the originals are untouched.
- The merge is shallow — nested objects are replaced, not deep-merged.

### `%values` — property values of an object

Return the own enumerable property values of an object as an array.

**Signature:** `%values <object> -> array`

**Transform**

```odin
{out}
v = %values @.obj
```

**In**

```odin
{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.v[] : ~}
"Ada"
"admin"
?true
```

**Notes**

- The single argument is an object; the result is an array of values serialized as a {.v[] : ~} block, preserving each value's type.
- Values follow source key order; prototype-pollution keys are excluded.
- A non-object argument resolves to ~.

## Idioms

Patterns that compose the cards above into real transforms.

### Format conversion

ODIN is the canonical model between every format. Changing only the direction header and `{$target} format` re-targets the output; the mapping body is unchanged.

### odin->csv — emit CSV rows from an ODIN source

Convert an ODIN array to CSV by setting the target format to csv; a [] section with _loop emits one row per element under a header row.

**Setup:** `direction = "odin->csv"; {$target} format="csv" header="true" delimiter=","`

**Transform**

```odin
{rows[]}
_loop = "@products"
sku = @.sku
name = @.name
price = @.price
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{products[] : sku, name, price}
"SKU-1", "Widget", #$9.99
"SKU-2", "Gadget", #$19.50
```

**Out**

```csv
sku,name,price
SKU-1,Widget,9.99
SKU-2,Gadget,19.5
```

**Notes**

- Only the header changes versus odin->odin: direction = "odin->csv" and {$target} format = "csv".
- {$target} header = "true" writes a header row from the field names; set header = "false" to omit it.
- {$target} delimiter overrides the column separator (e.g. "|" for pipe-separated output); it defaults to a comma.
- The [] section with _loop = "@products" iterates the source array; each mapped field becomes a column, @.field reading the current element.
- Currency values are emitted as plain decimals (9.99, 19.5) — type prefixes are dropped in the flat CSV form.
- The reverse path csv->odin uses direction = "csv->odin" with {$source} format = "csv"; the header row supplies field names and values are typed on ingest.

**Avoid**

- `{rows[]} → _from = "@products" → sku = @.sku` — _from alone does not iterate; a [] section needs _loop = "@products" to emit one row per element, otherwise the array collapses to a single broken row

### odin->fixed-width — emit fixed-width records from an ODIN source

Convert an ODIN array to fixed-width text by placing each field at an explicit column with :pos/:len and a pad direction.

**Setup:** `direction = "odin->fixed-width"; {$target} format="fixed-width"`

**Transform**

```odin
{rows[]}
_loop = "@employees"
id = %formatInteger @.id :pos 0 :len 5 :leftPad "0"
name = @.name :pos 5 :len 10 :rightPad " "
dept = @.dept :pos 15 :len 3
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{employees[] : id, name, dept}
##101, "SMITH", "ENG"
##102, "DOE", "MKT"
```

**Out**

```fixed-width
00101SMITH     ENG
00102DOE       MKT
```

**Notes**

- Only the header changes versus odin->odin: direction = "odin->fixed-width" and {$target} format = "fixed-width".
- Each field carries :pos (start column, 0-based) and :len (width); the field is placed at that fixed window in every record.
- :leftPad "0" pads numbers on the left (right-justified, e.g. id 101 -> 00101); :rightPad " " pads strings on the right (left-justified, e.g. SMITH -> 'SMITH     ').
- %formatInteger renders the numeric value as digits before padding so it fills the column cleanly.
- The [] section with _loop emits one fixed-width line per source element; @.field reads the current element.
- The reverse path fixed-width->odin uses direction = "fixed-width->odin" with {$source} format = "fixed-width"; each field's :pos/:len slices the input columns back into typed values.

### odin->json — emit JSON from an ODIN source

Convert an ODIN document to JSON by setting the target format to json, showing a nested object alongside a :loop array.

**Setup:** `direction = "odin->json"; {$target} format="json" indent="2"`

**Transform**

```odin
{order}
id = @order.id
customer = %upper @order.customer

{lines[]}
_loop = "@lines"
sku = @.sku
qty = @.qty
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{order}
id = "ORD-7"
customer = "acme"
{lines[] : sku, qty}
"A1", ##2
"B2", ##5
```

**Out**

```json
{
  "order": {
    "id": "ORD-7",
    "customer": "ACME"
  },
  "lines": [
    {
      "sku": "A1",
      "qty": 2
    },
    {
      "sku": "B2",
      "qty": 5
    }
  ]
}
```

**Notes**

- Only the header changes versus odin->odin: direction = "odin->json" and {$target} format = "json".
- {$target} indent = "2" pretty-prints with two-space indentation; omit it for compact JSON.
- A scalar section like {order} reads the source with full paths (@order.id); a section ending in [] with _loop iterates an array and emits a JSON array.
- Verbs and type coercion still apply on the way out (here %upper uppercases the customer name).
- The reverse path json->odin uses direction = "json->odin" with {$source} format = "json"; JSON types are inferred into ODIN types on ingest.

**Avoid**

- `{order} → id = @.id` — outside a :loop there is no current element, so @.id resolves to null; use the full source path @order.id in scalar sections

### odin->xml — emit XML from an ODIN source

Convert an ODIN document to XML by setting the target format to xml. The source stays ODIN; verbs and type coercion still apply on the way out.

**Setup:** `direction = "odin->xml"; {$target} format="xml" root="invoice"`

**Transform**

```odin
{invoice}
number = @.number
customer = %upper @.customer
total = #$@.total
```

**In**

```odin
number = "INV-100"
customer = "acme corp"
total = #$1250.00
```

**Out**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<invoice xmlns:odin="https://odin.foundation/ns">
  <number>INV-100</number>
  <customer>ACME CORP</customer>
  <total odin:type="currency">1250.00</total>
</invoice>
```

**Notes**

- Only the header changes versus odin->odin: direction = "odin->xml" and {$target} format = "xml".
- {$target} root = "invoice" names the XML root element.
- ODIN types survive the conversion: the currency field carries odin:type="currency" so the value round-trips.
- The reverse path xml->odin uses direction = "xml->odin" with {$source} format = "xml"; the odin:type attributes are read back to restore ODIN types.

**Avoid**

- `{$target} → format = "xml"   ; no root` — without root the element name falls back to the section header; set root explicitly for a stable document element

<!-- PLACEHOLDER: odin->json, odin->csv, odin->fixed-width (and reverse xml->odin, json->odin, csv->odin, fixed-width->odin) format-conversion idioms to be added. -->

### Patterns

### Maintain a running total across loop iterations with an accumulator

Declare a {$accumulator}, add each element's value inside a :loop with %accumulate, then read the final sum back via @$accumulator.*.

**Setup:** `{$accumulator} + %accumulate inside a [] _loop + @$accumulator.* read-back`

**Transform**

```odin
{$accumulator}
runningTotal = ##0
runningTotal._persist = true

{lines[]}
_loop = "@items"
name = @.name
amount = @.amount
_ = %accumulate runningTotal @.amount

{summary}
total = "@$accumulator.runningTotal"
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{items[] : name, amount}
"Widget", #$10.00
"Gadget", #$25.00
"Tool", #$5.00
```

**Out**

```json
{
  "lines": [
    {
      "name": "Widget",
      "amount": 10
    },
    {
      "name": "Gadget",
      "amount": 25
    },
    {
      "name": "Tool",
      "amount": 5
    }
  ],
  "summary": {
    "total": 40
  }
}
```

**Notes**

- {$accumulator} declares the variable and its seed; runningTotal._persist = true keeps the value across the whole document so a later section can read it.
- Inside the loop, _ = %accumulate runningTotal @.amount adds the current element's amount; the _ target discards the verb's own return while the accumulator updates.
- After the loop, @$accumulator.runningTotal reads the final total (10 + 25 + 5 = 40) into the summary section.
- The summary section is declared after the loop section so it runs once the accumulation is complete.

### Classify each element of an array via a lookup table

Iterate an array, mapping a code on each element through a lookup table, and emit one output row per element.

**Setup:** `{Section[]} + _loop/_from + %lookup`

**Transform**

```odin
{$table.STATUS[code, label]}
"A", "Active"
"P", "Pending"
"C", "Cancelled"

{policies[]}
_loop = "@"
_from = "policies"
number = @.number
status = %lookup "STATUS.label" @.statusCode
```

**In**

```odin
{policies[] : number, statusCode}
"POL-1", "A"
"POL-2", "P"
"POL-3", "C"
```

**Out**

```odin
{policies[] : number, status}
"POL-1", "Active"
"POL-2", "Pending"
"POL-3", "Cancelled"
```

**Notes**

- An output section ending in [] becomes a loop: _loop = "@" with _from = "policies" iterates that source array.
- Inside the loop, @.field reads the current element (here @.number and @.statusCode).
- Uniform rows are emitted as a compact tabular block ({policies[] : number, status}).

**Avoid**

- `number = @policies.number` — inside a loop use @.field for the current element; @policies.number reads the source root, not the element

### Select one of several sections with an :if / :elif / :else chain

Emit exactly one branch from a mutually exclusive chain of sections guarded by verb-expression conditions.

**Setup:** `{A :if <cond>} / {B :elif <cond>} / {C :else}`

**Transform**

```odin
{Driver}
name = @driver.name

{HighRisk :if %eq @driver.tier "dui"}
band = "high-risk"

{YoungDriver :elif %lt @driver.age ##25}
band = "young-driver"

{Standard :else}
band = "standard"
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{driver}
name = "Sam Cruz"
tier = "standard"
age = ##40
```

**Out**

```json
{
  "Driver": {
    "name": "Sam Cruz"
  },
  "Standard": {
    "band": "standard"
  }
}
```

**Notes**

- The :if / :elif / :else sections form one mutually exclusive chain; the engine emits the first section whose condition is true and skips the rest.
- Conditions are verb expressions: %eq @driver.tier "dui" and %lt @driver.age ##25 evaluate against the source.
- Here tier is "standard" (not "dui") and age 40 is not < 25, so both guards fail and the :else branch (Standard) is emitted.
- Unguarded sections like {Driver} always emit; only the :if/:elif/:else chain is conditional.
- A non-matching branch produces no key at all in the output, not an empty object.

### Emit verbatim lines from a :literal block with ${...} interpolation

Use a :literal section with a triple-quoted body to write fixed text, interpolating fields and verb results via ${...}.

**Setup:** `{Section} :literal + """...""" body with ${...} interpolation`

**Transform**

```odin
{HDR}
:literal
"""
HDR|${@policy.number}|${%upper @policy.code}
"""

{DET[]}
:loop @items
:literal
"""
DET|${@.sku}|${@.qty}
"""
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{policy}
number = "P-100"
code = "abc"
{items[] : sku, qty}
"A1", "2"
"B2", "5"
```

**Out**

```fixed-width
HDR|P-100|ABC
DET|A1|2
DET|B2|5
```

**Notes**

- A :literal section emits its triple-quoted body verbatim instead of building structured fields.
- ${...} interpolates an expression into the line: ${@policy.number} reads a field, ${%upper @policy.code} runs a verb (abc -> ABC).
- A :literal section can also :loop; here {DET[]} :loop @items repeats the body once per item with @.sku / @.qty bound to the current element.
- Escape a literal dollar-brace with a backslash (\${...}) to emit ${...} as text rather than interpolating it.
- Literal blocks suit delimited or templated line formats where exact characters matter, such as fixed-width or EDI-style records.

### Produce a cross-product with two nested :loop directives

Iterate an outer array and, for each element, an inner array, emitting one flattened row per (outer, inner) pair.

**Setup:** `{rows[]} + :loop <outer> :as a + :loop .inner :as b`

**Transform**

```odin
{rows[]}
:loop policy.vehicles :as veh
:loop .coverages :as cov
vehicle_vin = "@veh.vin"
coverage_code = "@cov.code"
coverage_limit = "@cov.limit"
```

**In**

```odin
{$}
odin = "1.0.0"
{}
{policy}
number = "POL-100"
{policy.vehicles[0]}
vin = "VIN-A"
{policy.vehicles[0].coverages[] : code, limit}
"LIAB", #$100000.00
"COLL", #$50000.00
{policy.vehicles[1]}
vin = "VIN-B"
{policy.vehicles[1].coverages[] : code, limit}
"COMP", #$25000.00
```

**Out**

```json
{
  "rows": [
    {
      "vehicle_vin": "VIN-A",
      "coverage_code": "LIAB",
      "coverage_limit": 100000
    },
    {
      "vehicle_vin": "VIN-A",
      "coverage_code": "COLL",
      "coverage_limit": 50000
    },
    {
      "vehicle_vin": "VIN-B",
      "coverage_code": "COMP",
      "coverage_limit": 25000
    }
  ]
}
```

**Notes**

- The first :loop names the outer array (policy.vehicles :as veh); the second :loop is relative (.coverages) to the current outer element and binds :as cov.
- Each :as alias is read with its own @alias.field — @veh.vin for the outer element, @cov.code / @cov.limit for the inner.
- The result is the cross-product: VIN-A has two coverages (two rows), VIN-B has one (one row), for three rows total.
- A relative inner path (.coverages) resolves against the current outer element, so each vehicle contributes only its own coverages.

<!-- PLACEHOLDER: additional pattern idioms to be added. -->

## Error catalog

Each transform error code, the minimal mistake that triggers it, and the fix.

### T001 — unknown verb

Calling a verb name that is not a registered built-in (and not a %& custom verb) fails the field.

**Trigger:** %notAVerb is not a known built-in verb.

**Transform**

```odin
{out}
x = %notAVerb @.a
```

**Fix:** Use a registered verb name, or define it as a custom verb (%&namespace.verb).

> **Note:** documented in the spec but the engine does not yet emit this stable code; see the notes below.

- The transform fails and an error is reported for the field.
- The engine currently reports this as code TRANSFORM_ERROR with the message 'Unknown verb: notAVerb'; the stable T001 code is not yet emitted.
- An unregistered %& custom verb does not raise this — it echoes its first argument as an extension point.

### T002 — invalid verb arguments

Passing an argument of the wrong type to a verb under strict type checking fails the transform.

**Trigger:** %sqrt receives a string where a number is required, with strictTypes enabled.

**Transform**

```odin
{out}
x = %sqrt "notanumber"
```

**Fix:** Pass a numeric argument (e.g. %sqrt @.value where value is a number), or coerce first with %coerceNumber.

- Requires strictTypes = ?true in the {$} header — without strict mode the wrong-typed argument is coerced instead of rejected.
- With strict mode the transform throws a type error carrying the T002 code on the thrown error; the message reads 'Type error in %sqrt: Arg 1: expected number, got string'.
- Because the thrown message text does not contain the literal string T002, the catalog documents this case rather than asserting on the message; the stable T002 code is present on the error object but not in its message.
- Supplying the wrong NUMBER of arguments is caught earlier at parse time with a distinct message, not as a coded T002.

### T003 — lookup table not found

Referencing a $table that was never declared makes the lookup unresolvable.

**Trigger:** GHOST is not a declared {$table.GHOST[...]} table.

**Transform**

```odin
{out}
x = %lookup "GHOST.code" @.k
```

**Fix:** Declare the table (e.g. {$table.GHOST[name, code]} with rows) before the lookup, or correct the table name.

> **Note:** documented in the spec but the engine does not yet emit this stable code; see the notes below.

- Requires onMissing = "fail" in the {$target} header; the default policy is silent (returns null, no error).
- The engine currently reports a missing table through the same path as a missing key, emitting code T004 ('Lookup key '...' not found in table 'GHOST''); a distinct T003 code is not yet emitted.

### T004 — lookup key not found

A %lookup whose match value has no matching row in the table fails when onMissing is fail.

**Trigger:** 'nope' is not present in the STATUS table's name column.

**Transform**

```odin
{$table.STATUS[name, code]}
"active", "A"

{out}
x = %lookup "STATUS.code" @.k
```

**Fix:** Add the missing row to the table, use %lookupDefault to supply a fallback, or set onMissing = "warn".

- Requires onMissing = "fail" in the {$target} header; the default policy is silent (returns null, no error).
- Reported as a T004 error: 'Lookup key 'nope' not found in table 'STATUS''.
- Setting onMissing = "warn" demotes the same T004 to a warning instead.

### T005 — source path not found

A field marked :required whose source path resolves to nothing fails the transform.

**Trigger:** The path does.not.exist is absent from the source and the field is :required.

**Transform**

```odin
{out}
x = @.does.not.exist :required
```

**Fix:** Provide the source path, drop :required, or supply a default with %coalesce / %ifNull.

> **Note:** documented in the spec but the engine does not yet emit this stable code; see the notes below.

- Without :required a missing path yields null silently; the :required modifier is what raises the error.
- The transform fails and an error is reported for the field.
- The engine currently reports this as code SOURCE_MISSING with the message 'Required field 'x' is missing or null'; the stable T005 code is not yet emitted.

### T006 — invalid output format

Declaring a target format the engine does not recognize should fail the transform.

**Trigger:** format = "notaformat" in {$target} is not a registered output formatter.

**Transform**

```odin
{out}
x = @.a
```

**Fix:** Set {$target} format to a supported value: odin, json, xml, csv, or fixed-width.

> **Note:** documented in the spec but the engine does not yet emit this stable code; see the notes below.

- An unknown target format is not rejected. The engine currently falls back to JSON-serializing the canonical form and reports success with no errors or warnings; the stable T006 code is not yet emitted.
- Use a supported output format to get predictable results.

### T007 — invalid modifier for format

Applying a format-specific modifier (e.g. fixed-width :pos / :len) to a target that does not support it.

**Trigger:** :pos and :len are fixed-width positioning modifiers but the target format is json.

**Transform**

```odin
{out}
x = @.a :pos 0 :len 5
```

**Fix:** Drop the fixed-width modifiers for JSON output, or switch the target format to fixed-width.

- Reported as a T007 warning, one per incompatible modifier: 'Modifier ':pos' is not applicable to format 'json' and will be ignored'.
- The transform still succeeds; the offending modifiers are dropped from the output.

### T008 — accumulator overflow

An accumulator whose running value exceeds engine limits should fail the transform.

**Trigger:** Accumulating values past the accumulator's numeric capacity.

**Transform**

```odin
{$accumulator}
total = ##0

{out}
x = %accumulate "total" @.a
```

**Fix:** Reduce the magnitude of accumulated values or use a wider numeric representation.

> **Note:** documented in the spec but the engine does not yet emit this stable code; see the notes below.

- The engine currently enforces no overflow bound on %accumulate; the transform succeeds and the stable T008 code is not yet emitted.
- Documented for completeness so the catalog covers every defined transform error code.

### T009 — loop source not array

A :loop whose source path resolves to a scalar instead of an array should fail.

**Trigger:** notArr resolves to the string "scalar", not an array, under :loop.

**Transform**

```odin
{out[]}
:loop notArr
x = @.a
```

**Fix:** Point :loop at an array path, or wrap the scalar in an array before looping.

> **Note:** documented in the spec but the engine does not yet emit this stable code; see the notes below.

- When the loop source is not an array the engine currently skips iteration and emits an empty result; it reports success and the stable T009 code is not yet emitted.
- Ensure the :loop path resolves to an array so each element is iterated.

### T010 — fixed-width position overflow

A fixed-width field whose :pos + :len extends past the declared lineWidth overflows the line.

**Trigger:** :pos 78 plus :len 10 = 88 exceeds the lineWidth of 80.

**Transform**

```odin
{segment.HDR}
rec = @.a :pos 78 :len 10
```

**Fix:** Lower :pos or :len so :pos + :len <= lineWidth, or widen lineWidth.

- Requires a fixed-width target with lineWidth set in {$target}.
- Reported as a T010 warning: 'Field at position 78 with length 10 exceeds line width 80'.
- The transform still succeeds; behavior is governed by the onOverflow policy, which warns by default.

### T011 — incompatible or unknown conversion target

Passing an unrecognized unit to a conversion verb fails the field.

**Trigger:** %distance is given the unit "parsecs", which is not a supported distance unit.

**Transform**

```odin
{out}
x = %distance ##0 ##0 ##1 ##1 "parsecs"
```

**Fix:** Use a supported unit: "km", "mi", or "miles".

- Reported as a T011 error: 'Incompatible conversion in 'distance': unknown unit 'parsecs' (expected 'km', 'mi', or 'miles')'.
- %dateDiff with an unknown unit (e.g. "fortnights") raises the same T011.

### T012 — dangling :elif / :else

An :elif or :else conditional segment with no preceding :if segment is a dangling branch.

**Trigger:** The {out} segment carries :else but no :if segment precedes it.

**Transform**

```odin
{out}
:else
x = @.a
```

**Fix:** Add a preceding :if segment to open the conditional chain, or remove the :else.

- Reported as a T012 error: ''else' segment has no preceding 'if''.
- A dangling :elif raises the same T012 ('elif' segment has no preceding 'if').

### T013 — field validation failure

A field value that violates an :enum / :validate / :range constraint fails validation.

**Trigger:** The value "blue" is not in the allowed :enum set red,green.

**Transform**

```odin
{out}
x = @.a :enum "red,green"
```

**Fix:** Supply a value within the constraint, widen the :enum/:range, or set onValidation = "warn"/"skip".

- Reported as a T013 error under the default onValidation = fail policy: 'Validation failed for 'x': value 'blue' is not one of [red, green]'.
- :range uses the dotted form (e.g. :range 1..10) and :validate takes a regex; all three raise T013 the same way.
- onValidation = "warn" demotes the T013 to a warning; "skip" drops the field silently.

### T014 — nested interpolation

A ${...} interpolation whose expression itself contains another ${ inside a :literal block is rejected.

**Trigger:** The literal-block interpolation ${@a.${@b}} nests a second ${ inside the first.

**Transform**

```odin
{X}
:literal
"""
${@a.${@b}}
"""
```

**Fix:** Flatten the expression so each ${...} stands alone; compute the inner value in a prior field if needed.

- Only :literal segment blocks (triple-quoted body) enforce the no-nesting rule.
- Reported as a T014 error: 'Nested interpolation is not allowed: ${@a.${@b}'.
