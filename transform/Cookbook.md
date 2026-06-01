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

## Numeric

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

## Date & Time

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

## Array

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

## Logic & Conditionals

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

## Type Coercion

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

## Encoding

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

## Idioms

Patterns that compose the cards above into real transforms.

### Format conversion

ODIN is the canonical model between every format. Changing only the direction header and `{$target} format` re-targets the output; the mapping body is unchanged.

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

<!-- PLACEHOLDER: additional pattern idioms to be added. -->

## Error catalog

A catalog of common transform errors and their fixes.

<!-- PLACEHOLDER: error catalog to be filled later. -->
