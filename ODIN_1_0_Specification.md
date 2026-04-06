# ODIN 1.0

**Open Data Interchange Notation**

---

## Overview

ODIN is a data interchange format designed for the AI era—combining the token efficiency of CSV, the nesting capability of JSON, the type safety of Protocol Buffers, and the human readability of TOML, all in a single notation that machines and humans can parse, generate, and reason about without ambiguity.

ODIN provides:
- **Self-describing typed values** via prefixes (`#`, `?`, `@`, `^`, `~`, `#$`, `##`, `%`) that eliminate parsing ambiguity
- **Inline modifiers** (`!` required, `*` confidential, `-` deprecated) that travel with values, not in separate schema
- **Tabular mode** that eliminates key repetition for lists, cutting payload 20-50% vs JSON
- **Strict syntax** with one valid parse—no barewords, no leniency, no edge cases
- **Mixed modes** in one document: flat assignments, nested paths, typed tables

No existing format combines all of these. ODIN fills the gap between human-readable configuration formats and machine-efficient data interchange—optimized for workflows where humans and AI collaborate on structured data.

ODIN is a specification, not a standard. Standards imply committees, politics, and "implementation flexibility" that defeats interoperability. ODIN is a notation—one way to write it, one way to read it.

---

## License

**Specification:** Creative Commons Attribution 4.0 International (CC-BY 4.0)

**Reference Implementations:** Apache License 2.0

You are free to:
- **Use** — commercially or non-commercially, without restriction
- **Share** — copy and redistribute in any medium or format
- **Adapt** — remix, transform, and build upon for any purpose

Under the following terms:
- **Attribution** — You must give appropriate credit, provide a link to the license, and indicate if changes were made

Full license texts available at:
- https://creativecommons.org/licenses/by/4.0/
- https://www.apache.org/licenses/LICENSE-2.0

---

## Design Principles

1. **Self-describing** - Types are in the data, not just schema
2. **Line-based** - One assignment per line, no wrapping
3. **Flat** - Explicit paths, no implicit tree traversal
4. **Diffable** - Meaningful diffs in version control
5. **Token-efficient** - Minimal structural overhead
6. **Deterministic** - Same data produces same output
7. **Composable** - Documents can chain and reference each other

---

## Design Philosophy

### Why Not a Standard?

ODIN is deliberately not a standard. Standards imply committees, politics, versioning battles, and "implementation flexibility" that defeats the entire purpose of interoperability. Every format that allows implementers to choose between options creates `n!` possible implementations—the opposite of interchange.

ODIN is a notation: one way to write it, one way to read it. Linux isn't an ISO standard. JSON achieved ubiquity years before ECMA-404. Organic adoption through utility beats institutional blessing. If ODIN is useful, people will use it. If it's not, no committee can save it.

### Why Symbol Density?

ODIN uses prefix symbols (`#`, `##`, `#$`, `?`, `@`, `^`, `~`, `%`, `!`, `*`, `-`) rather than keywords. This is intentional:

**For machines:** Symbols are unambiguous single-token markers. A parser sees `#` and knows "number follows" without lookahead. No reserved words to conflict with data values. No quoting rules that vary by context.

**For AI/LLM processing:** A format designed for 2025 should assume AI is in the processing pipeline. JSON's structural overhead—braces, brackets, colons, quotes around every key—is token waste. Compare:

```json
{"vehicle": {"year": 2022, "make": "Honda", "model": "Accord"}}
```

```odin
vehicle.year = #2022
vehicle.make = "Honda"
vehicle.model = "Accord"
```

The ODIN version is more tokens of actual data, fewer tokens of syntax. When you're processing millions of documents through LLMs, this matters.

**For humans:** Yes, there's a learning curve. But symbols are faster to scan once learned—the same reason mathematicians use `∑` instead of "the sum of." ODIN isn't optimized for first-time readers; it's optimized for repeated use.

### Why Flat Paths?

Nested formats like JSON and XML have a fundamental problem: merge conflicts. When two branches modify different fields in the same object, the entire object block conflicts because the closing braces don't match up.

ODIN's flat paths eliminate this:

```odin
; Branch A adds:
policy.coverage.collision = #500

; Branch B adds:
policy.coverage.comprehensive = #250

; Git merges cleanly—different lines, no conflict
```

The tradeoff is verbosity—repeating `policy.coverage` on every line. Headers solve this:

```odin
{policy.coverage}
collision = #500
comprehensive = #250
liability = #100000
```

Headers provide visual grouping for humans while maintaining the flat, diffable structure underneath. The canonical form (sorted, no headers) is what machines compare; the authored form (with headers) is what humans write.

### Why Self-Describing Types?

In JSON, `"42"` and `42` are different types, but `42` and `42.0` might or might not be depending on the parser. Dates are strings. Booleans are sometimes strings. Currency values lose precision through floating-point representation. The actual types live in external schemas that may or may not match the data.

ODIN types are in the data itself:

```odin
count = ##42          ; explicitly integer
price = #$99.99       ; explicitly currency  
rate = #0.0525        ; explicitly numeric
active = ?true        ; explicitly boolean
created = 2024-06-15  ; explicitly date (ISO 8601 pattern)
```

A parser doesn't need a schema to know what type each value is. Schemas add constraints (ranges, patterns, required fields), but the base types are self-evident. This eliminates an entire class of bugs where schema and data disagree about types.

### Why Deterministic Canonical Form?

Two ODIN documents with the same logical content must produce identical byte sequences in canonical form. This enables:

- **Content addressing:** Hash the document, get a stable identifier
- **Deduplication:** Identical data has identical hashes
- **Verification:** Signature covers exactly what you see
- **Caching:** Same input, same output, safe to cache

This requires strict rules: sorted paths, no comments, no blank lines, LF line endings, quoted strings. The rules are simple enough that any implementation can produce identical output—unlike JSON, where key ordering, whitespace, and Unicode escaping vary by library.

### Why Inline Modifiers?

Most formats separate metadata from data. JSON Schema lives in a different file. XML attributes are distinct from element content. This creates synchronization problems—the schema says "required" but the data doesn't know it.

ODIN modifiers travel with the value:

```odin
{customer}
name = !"John Smith"           ; required string
ssn = *"123-45-6789"           ; confidential — value is stored, modifier signals sensitivity
email = !*"john@example.com"   ; required AND confidential
phone = "555-1234"             ; optional, not sensitive
```

**For validation:** A parser can check required fields without loading a separate schema. The `!` modifier is self-contained.

**For privacy:** Fields marked `*` carry the confidential signal with the data itself. Downstream systems — loggers, error reporters, API gateways, AI pipelines — can detect the modifier and handle the value accordingly (mask, redact, encrypt, or restrict access) without external configuration.

**For AI/LLM workflows:** When an LLM generates or processes data, it sees the constraints inline. It knows which fields it must populate, which contain sensitive data that shouldn't be echoed back, and which are deprecated. No separate schema lookup required.

### Why This Combination?

Existing formats fall into distinct camps:

- **Tabular-first (CSV, TSV, Parquet):** Great for flat rows, but no nesting, no types, no schema
- **Nested-first (JSON, YAML, TOML):** Great for hierarchies, but tabular data becomes verbose arrays with repeated keys
- **Schema-first (Protobuf, Avro):** Type-safe but binary, with schema separate from data

ODIN combines all three in one format:

```odin
{order}
id = "ORD-001"
customer.name = "Acme Corp"
placed = 2025-01-15T09:30:00Z

{order.items[] : sku, name, qty, price}
"W-100", "Widget", ##10, #$29.99
"G-200", "Gadget", ##5, #$149.50

{order.shipping}
address = @addresses[0]
method = "express"
cost = #$15.00
```

One document with:
- Flat assignments (`id = "ORD-001"`)
- Nested paths (`customer.name`)
- Typed tabular data (no key repetition in rows)
- Type references (`@addresses[0]`)
- Native dates, currency, integers
- All human-readable, all parseable

**Token efficiency:** ODIN achieves an average 38% size reduction compared to equivalent JSON, without loss of data integrity or fidelity. Larger files with repetitive structures may see reductions upwards of 50%. When processing data through LLMs or transmitting over APIs, this translates directly to cost and latency savings.

Existing formats do not provide typed, tabular-capable, nested, token-efficient, human-readable data interchange in a single notation. ODIN fills this gap—optimized for workflows where humans and machines collaborate on structured data.

---

## Encoding

| Property | Value |
|----------|-------|
| Character set | UTF-8 |
| Line endings | LF (U+000A) preferred, CRLF (U+000D U+000A) accepted |
| Line length | Unlimited (no wrapping) |
| Whitespace | Spaces (U+0020) and tabs (U+0009) for indentation/alignment |
| BOM | UTF-8 BOM (U+FEFF) should be ignored if present, not recommended for output |
| Normalization | No normalization required; bytes are compared as-is |

### Case Sensitivity

ODIN is **case-sensitive** throughout:

- Path segments: `Name` and `name` are different fields
- Type keywords: `date`, `timestamp`, `time`, `duration` must be lowercase
- Boolean literals: `true` and `false` must be lowercase
- Enum values: case is preserved and significant

### Reserved Words

ODIN has **no reserved words**. Field names can be any valid identifier, including:

```odin
true = "some value"        ; valid - 'true' is a field name, not boolean
date = 2024-06-15          ; valid - 'date' is a field name, value is a date
null = ~                   ; valid - 'null' is a field name, value is null
```

Type detection uses prefixes and patterns, not keywords:
- `?true` or `?false` with `?` prefix → boolean
- ISO 8601 date pattern (`YYYY-MM-DD`) → date
- Quoted values → string

**Note:** Parsers may accept bare `true`/`false` without the `?` prefix for compatibility, but the `?` prefix is the canonical form and should always be used when writing ODIN.

---

## Arrays

Arrays are ordered collections accessed by zero-based index.

### Array Creation

Arrays are created implicitly by assigning to indexed paths:

```odin
items[0].name = "First"
items[0].price = #10.00
items[1].name = "Second"
items[1].price = #20.00
```

### Array Rules

1. **Zero-based** — First element is `[0]`
2. **Contiguous** — Indices must be sequential with no gaps
3. **Object elements** — Array elements are objects with fields, not primitive values
4. **Implicit creation** — Assigning to `field[0]` creates the array

### Empty and Null Arrays

```odin
items[] = ~                ; explicit empty/null array (no elements)
```

An absent array path means the array doesn't exist. An explicit `field[] = ~` means the array exists but is empty (important for document chaining where you want to clear an inherited array).

### Sparse Arrays (Invalid)

```odin
; INVALID - indices must be contiguous
items[0].name = "First"
items[5].name = "Fifth"    ; error: missing indices 1-4
```

### Arrays of Primitives

ODIN arrays contain objects, not primitive values. For a list of simple values, use an object wrapper:

```odin
; Array of tag objects (each with a value field)
tags[0].value = "urgent"
tags[1].value = "review"
tags[2].value = "approved"

; Or use comma-separated string for simple cases
tags = "urgent, review, approved"
```

### Tabular Syntax

For arrays of flat objects (primitives only), tabular syntax provides a compact representation:

```odin
{line_items[] : sku, description, qty, price}
"ABC-001", "Widget", ##10, #$5.99
"ABC-002", "Gadget", ##5, #$12.50
"XYZ-100", "Cable, 6ft", ##20, #$3.25
```

This is equivalent to:

```odin
line_items[0].sku = "ABC-001"
line_items[0].description = "Widget"
line_items[0].qty = ##10
line_items[0].price = #$5.99
line_items[1].sku = "ABC-002"
line_items[1].description = "Gadget"
line_items[1].qty = ##5
line_items[1].price = #$12.50
line_items[2].sku = "XYZ-100"
line_items[2].description = "Cable, 6ft"
line_items[2].qty = ##20
line_items[2].price = #$3.25
```

**Tabular Rules:**

1. **Header declares columns** — `{path[] : col1, col2, col3}` defines array path and column names
2. **Rows follow header** — Each non-blank, non-comment line is a row
3. **Comma-separated values** — Values separated by `,` with optional whitespace
4. **Standard type prefixes** — Values use `#`, `##`, `#$`, `@`, `^`, `~`, `true`, `false`, dates, etc.
5. **Quote strings with commas** — Use `"value, with comma"` for strings containing commas
6. **Null cells** — Use `~` for null values
7. **Absent cells** — Empty cell (nothing between commas) means field is absent (no path created)
8. **Exit tabular mode** — Next header `{...}` or document separator `---` ends tabular section
9. **Primitive arrays** — Use `{path[] : ~}` for arrays of primitive values (one value per line, no commas)

**Cell Value Semantics:**

| Syntax | Meaning | Path Created? |
|--------|---------|---------------|
| `~` | Null value | Yes (with null) |
| `""` | Empty string | Yes (with "") |
| *(empty)* | Absent/missing | No |

```odin
; Example showing null vs absent semantics
{items[] : name, description, notes}
"Widget", "A useful widget", "In stock"
"Gadget", ~, "Backordered"           ; description is null
"Gizmo", "",                          ; description is empty string, notes is absent
"Thing", ,                            ; description is absent, notes is absent
```

**Tabular Constraints:**

Tabular syntax is **only allowed** when:

- Array contains objects with **primitive fields only** at the column paths
- **Single-level nesting allowed** in column names (one dot or one array index)
- No multi-level nesting (no `a.b.c` or `a[0].b` paths)
- No nested arrays within columns
- Rows may have **different columns** (absent cells for missing fields)

**Relative Column Names:**

To reduce repetition when multiple columns share a common parent path, use **relative column names** with a leading dot (`.`):

- A column starting with `.` inherits the parent path of the previous explicit column
- The previous column must have a dot-notation path (e.g., `address.line1`)
- Columns without a leading `.` are absolute and reset the context

```odin
; These two headers are equivalent:
{holders[] : name, address.line1, address.city, address.state, address.postal, active}
{holders[] : name, address.line1, .city, .state, .postal, active}
```

**Rules:**

1. `.field` inherits from the previous column's parent (e.g., after `address.line1`, `.city` becomes `address.city`)
2. An absolute column (no leading `.`) resets the context
3. The first column cannot be relative (no previous context)
4. Relative columns after a non-dotted column are invalid

```odin
; ✓ VALID - relative columns after address.line1
{data[] : id, address.line1, .city, .state, type}
"001", "123 Main St", "Dallas", "TX", "home"

; ✓ VALID - multiple context switches
{data[] : name, home.street, .city, work.street, .city}
"John", "123 Oak Ave", "Dallas", "456 Elm St", "Austin"

; ✗ INVALID - relative column after non-dotted column
{data[] : name, .city}

; ✗ INVALID - first column cannot be relative
{data[] : .name, .city}
```

```odin
; ✓ VALID - all primitives
{drivers[] : name, license, dob, primary}
"John Smith", "DL-123456", 1985-03-15, ?true
"Jane Doe", "DL-789012", 1990-07-22, ?false

; ✓ VALID - includes references and nulls
{assignments[] : driver, vehicle, effective}
@drivers[0], @vehicles[0], 2025-01-01
@drivers[1], ~, ~

; ✓ VALID - single-level dot notation (one sub-object)
{items[] : product.name, product.sku, qty, price}
"Widget Pro", "WGT-001", ##10, #$29.99
"Cable 6ft", "CBL-006", ##25, #$4.99

; ✓ VALID - single-level array index
{users[] : name, permissions[0], permissions[1]}
"Admin", "read", "write"
"Guest", "read", ~

; ✓ VALID - relative column names (inheriting parent from previous column)
{holders[] : name, address.line1, .city, .state, .postal}
"ABC Corp", "500 Commerce St", "Dallas", "TX", "75201"
"XYZ LLC", "123 Main St", "Austin", "TX", "78701"

; ✗ INVALID - multi-level nesting (two dots)
{items[] : product.details.sku, product.details.name}

; ✗ INVALID - mixed nesting (dot + array index)
{orders[] : items[0].name, items[0].price}

; ✗ INVALID - nested array (column value would be array, not primitive)
{users[] : name, roles[]}
```

**Canonical Form:**

In canonical form, tabular syntax is **not used**. Canonical form always uses explicit indexed paths for deterministic output. Tabular syntax is an authoring convenience that parsers must expand to standard form.

### Primitive Array Tabular Syntax

For arrays containing only primitive values (no object fields), use the special `~` column marker:

**Syntax:** `{path[] : ~}`

This indicates "value-only mode" where each row contains a single primitive value:

```odin
; Integer array
{txIndexes[] : ~}
##8208220048659020
##2830423323628866
##3871696279527106

; String array
{tags[] : ~}
"urgent"
"important"
"reviewed"

; Mixed primitives
{values[] : ~}
"text"
##42
?true
~
```

This is equivalent to:

```odin
txIndexes[0] = ##8208220048659020
txIndexes[1] = ##2830423323628866
txIndexes[2] = ##3871696279527106

tags[0] = "urgent"
tags[1] = "important"
tags[2] = "reviewed"

values[0] = "text"
values[1] = ##42
values[2] = ?true
values[3] = ~
```

**Primitive Array Rules:**

1. **One value per line** — Each line after the header is a single primitive value
2. **No commas** — Unlike object tabular, values are not comma-separated
3. **Standard type prefixes** — Values use `##`, `#$`, `@`, `^`, `~`, `?true/false`, dates, quoted strings, etc.
4. **Null values** — `~` on a data row creates a null entry at that index
5. **Exit conditions** — Same as object tabular: next header `{...}` or assignment `path =` exits primitive array mode
6. **Empty arrays** — `{path[] : ~}` with no data rows produces an empty array

**When to use:**
- Arrays of integers (IDs, indices, counts)
- Arrays of strings (tags, labels, codes)
- Arrays of any single primitive type or mixed primitives

**When NOT to use:**
- Arrays of objects with named fields — use standard tabular `{path[] : col1, col2}`
- Arrays containing nested structures — use explicit indexed paths

```odin
; ✓ VALID - primitive integer array
{transactionIds[] : ~}
##1001
##1002
##1003

; ✓ VALID - primitive string array
{categories[] : ~}
"electronics"
"home"
"garden"

; ✓ VALID - nested primitive array in header context
{order}
id = "ORD-001"
{.lineNumbers[] : ~}
##1
##2
##3
```

---

## File Metadata

### File Extension

ODIN documents use the `.odin` file extension. The recommended naming conventions distinguish document types:

| Pattern | Purpose | Example |
|---------|---------|---------|
| `*.odin` | General ODIN data documents | `policy.odin`, `config.odin` |
| `*.schema.odin` | ODIN Schema definitions | `auto.schema.odin`, `person.schema.odin` |
| `*.transform.odin` | ODIN Transform definitions | `xml-to-odin.transform.odin`, `export.transform.odin` |

**Examples:**
```
; Data documents
policy.odin
customer.odin
config.odin

; Schema definitions
auto-policy.schema.odin
person.schema.odin
address.schema.odin

; Transform definitions
acord-to-odin.transform.odin
odin-to-json.transform.odin
import.transform.odin
```

These conventions enable:
- **Tooling**: IDEs and build systems can apply appropriate syntax highlighting and validation
- **Organization**: Clear distinction between data, schemas, and transforms in large projects
- **Discovery**: Glob patterns like `**/*.schema.odin` find all schemas in a project

### MIME Type

The recommended MIME type for ODIN documents is:

```
text/odin-l
```

With optional charset parameter (UTF-8 is default and recommended):

```
text/odin-l; charset=utf-8
```

For HTTP Content-Type headers:

```http
Content-Type: text/odin-l; charset=utf-8
```

---

## Comments

Comments begin with semicolon (`;`) and extend to end of line:

```odin
; This is a full-line comment
name = "John Smith"     ; This is an inline comment

; Comments can appear anywhere
{policy}                ; even after headers
effective = 2024-06-15  ; and after values
```

### Comment Rules

1. **Semicolon start** — Comments begin with `;` character
2. **Line-scoped** — Comments extend to end of line only
3. **No block comments** — Multi-line comments require `;` on each line
4. **Stripped in canonical** — Comments are removed in canonical form
5. **No escape** — Semicolons in quoted strings are not comments: `desc = "a; b"` is valid

### Directives

Lines starting with `@` are directives (import, schema, conditional):

```odin
; This is a comment
@import ./other.odin      ; Import directive
@schema https://...       ; Schema directive
@if condition             ; Conditional directive
```

Directives must appear at the start of a line (column 1). The `@` prefix is unambiguous because references (`@path`) only appear after `=` in assignments, never at line start.

---

## Security Considerations

### Input Validation

Implementations should guard against:

1. **Excessive nesting** — Limit path depth to prevent stack overflow
2. **Large documents** — Enforce size limits to prevent memory exhaustion
3. **Long strings** — Limit string length to prevent memory exhaustion
4. **Circular references** — Detect and reject reference cycles
5. **Path traversal** — Sanitize import paths to prevent file system access outside allowed directories

### Binary Data

The `^` (binary) type contains Base64-encoded data. Implementations should:

1. **Validate Base64** — Reject invalid Base64 encoding
2. **Limit size** — Enforce maximum decoded size
3. **Sandbox processing** — Treat decoded content as untrusted

### Verb Expressions

The `%` (verb) type represents transform expressions that are evaluated during data transformation. Verb expressions consist of a verb name followed by arguments:

```odin
; Basic verb with reference argument
result = %upper @name

; Verb with multiple arguments
fullName = %concat @firstName " " @lastName

; Nested verbs (inner verbs evaluate first)
result = %upper %concat @first " " @last

; Custom verbs use %& prefix
custom = %&myNamespace.customVerb @value
```

**Verb Expression Rules:**

1. **Verb prefix** — Standard verbs use `%`, custom verbs use `%&`
2. **Verb name** — Immediately follows prefix, no space (e.g., `%upper`, `%concat`)
3. **Arguments** — Space-separated after verb name
4. **Argument types** — Arguments can be references (`@path`), strings (`"literal"`), numbers (`#42`), booleans (`true`/`false`), null (`~`), or nested verb expressions (`%verb ...`)
5. **Arity** — Each verb has a defined number of arguments (arity); variadic verbs consume remaining arguments until end of line or modifier

**Implementation Notes:**

- Verb expressions are primarily used in ODIN Transform documents to define data mappings
- Parsers should use arity information to determine how many arguments to consume
- Unknown verbs default to variadic arity (-1), consuming all remaining arguments
- The quoted string syntax (`"%upper @name"`) remains valid for backward compatibility

See the ODIN Transform Specification for the complete list of built-in verbs and their arities.

### Import Security

When processing `@import` directives:

1. **Whitelist sources** — Only allow imports from trusted locations
2. **No remote by default** — Disable `https://` imports unless explicitly enabled
3. **Prevent loops** — Track import chain to prevent circular imports
4. **Sandbox file access** — Restrict filesystem access to designated directories

### Sensitive Data

Fields marked with `*` (confidential) modifier indicate sensitive content. The modifier does not mask or alter the value in the document — it is a signal to downstream consumers:

1. **Logging** — Downstream loggers should mask or omit confidential field values
2. **Error messages** — Do not include confidential values in error output
3. **Secure memory** — Consider secure memory handling for confidential values
4. **Encryption** — The confidential modifier does not encrypt; use application-level encryption for sensitive data at rest
5. **AI/LLM processing** — AI systems should not echo, repeat, or include confidential field values in responses, summaries, or generated outputs unless explicitly authorized

The `*` modifier serves as a machine-readable signal that a value is sensitive. Unlike human-readable comments or external policies, the modifier travels with the data itself, enabling automated privacy handling across system boundaries — including AI pipelines where sensitive data might otherwise be inadvertently exposed in model outputs.

```ebnf
(* Document Structure *)
document        = { line } , [ document_separator , document ] ;
line            = ( comment | directive | header | assignment | tabular_row | blank ) , EOL ;
blank           = { whitespace } ;
EOL             = LF | CRLF ;

(* Tabular Rows - valid only after tabular_header *)
tabular_row     = tabular_value , { "," , { whitespace } , tabular_value } ;
tabular_value   = typed_value ;

(* Document Separator *)
document_separator = "---" , EOL ;

(* Directives - must appear at line start *)
directive       = import_directive | schema_directive | conditional_directive ;
import_directive = "@import" , whitespace , import_path , [ whitespace , "as" , whitespace , identifier ] ;
import_path     = quoted_string ;
schema_directive = "@schema" , whitespace , url ;
conditional_directive = "@if" , whitespace , expression ;

(* Comments *)
comment         = { whitespace } , ";" , { any_char } ;

(* Headers *)
header          = standard_header | tabular_header ;
standard_header = "{" , [ header_path ] , "}" ;
tabular_header  = "{" , array_path , ":" , column_list , "}" ;
array_path      = path_segment , { "." , path_segment } , "[" , "]" ;
column_list     = column_name , { "," , { whitespace } , column_name } ;
column_name     = absolute_column | relative_column ;
absolute_column = identifier , [ "." , identifier | "[" , integer , "]" ] ;
relative_column = "." , identifier ;  (* inherits parent from previous column *)
header_path     = absolute_path | relative_path ;
absolute_path   = path_segment , { "." , path_segment } ;
relative_path   = "." , { "." , path_segment } ;

(* Assignments *)
assignment      = { whitespace } , path , { whitespace } , "=" , { whitespace } , value ;

(* Paths *)
path            = [ extension_prefix ] , path_segment , { "." , path_segment } ;
extension_prefix = "&" , reverse_domain , "." ;
reverse_domain  = domain_part , { "." , domain_part } ;
domain_part     = letter , { letter | digit | "-" } ;
path_segment    = identifier , [ array_index ] ;
identifier      = ( letter | "_" ) , { letter | digit | "_" } ;
array_index     = "[" , integer , "]" ;

(* Values *)
value           = [ modifiers ] , typed_value ;
modifiers       = critical , [ deprecated ] , [ redacted ] 
                | deprecated , [ redacted ]
                | redacted ;
critical        = "!" ;
redacted        = "*" ;
deprecated      = "-" ;

typed_value     = null_value
                | boolean_value
                | number_value
                | date_value
                | timestamp_value
                | time_value
                | duration_value
                | reference_value
                | binary_value
                | verb_expression
                | string_value ;

(* Typed Values *)
null_value      = "~" ;
boolean_value   = "?" , ( "true" | "false" ) ;
number_value    = number_prefix , number ;
number_prefix   = currency_prefix | integer_prefix | generic_number ;
currency_prefix = "#$" ;
integer_prefix  = "##" ;
generic_number  = "#" ;
date_value      = date ;
timestamp_value = timestamp ;
time_value      = time ;
duration_value  = duration ;
reference_value = "@" , path ;
binary_value    = "^" , [ algorithm , ":" ] , base64 ;

(* Numbers *)
number          = [ "-" ] , integer , [ "." , digits ] , [ exponent ] ;
integer         = "0" | ( digit_nonzero , { digit } ) ;
digits          = digit , { digit } ;
exponent        = ( "e" | "E" ) , [ "+" | "-" ] , digits ;

(* Dates and Times - ISO 8601 *)
date            = year , "-" , month , "-" , day ;
timestamp       = date , "T" , time_part , [ timezone ] ;
time            = "T" , time_part ;
time_part       = hour , ":" , minute , ":" , second , [ "." , fraction ] ;
timezone        = "Z" | ( ( "+" | "-" ) , hour , ":" , minute ) ;
year            = digit , digit , digit , digit ;
month           = digit , digit ;
day             = digit , digit ;
hour            = digit , digit ;
minute          = digit , digit ;
second          = digit , digit ;
fraction        = digits ;

(* Duration - ISO 8601 *)
duration        = "P" , [ years ] , [ months ] , [ days ] , [ time_duration ] ;
years           = integer , "Y" ;
months          = integer , "M" ;
days            = integer , "D" ;
time_duration   = "T" , [ hours ] , [ minutes ] , [ seconds ] ;
hours           = integer , "H" ;
minutes         = integer , "M" ;
seconds         = number , "S" ;

(* Binary *)
algorithm       = letter , { letter | digit | "-" } ;
base64          = { base64_char } , [ "=" , [ "=" ] ] ;
base64_char     = letter | digit | "+" | "/" ;

(* Verb Expressions *)
verb_expression = verb_prefix , verb_name , { whitespace , verb_argument } ;
verb_prefix     = "%&" | "%" ;
verb_name       = identifier ;
verb_argument   = reference_value | string_value | number_value | boolean_value | null_value | verb_expression ;

(* Strings *)
string_value    = quoted_string ;

quoted_string   = '"' , { quoted_char } , '"' ;
quoted_char     = unescaped_quoted | escaped ;
unescaped_quoted = ? any UTF-8 character except "\" , '"' , newline ? ;
escaped         = "\" , escape_code ;
escape_code     = "\" | '"' | "n" | "t" | "r" | "0" | unicode_short | unicode_long ;
unicode_short   = "u" , hex , hex , hex , hex ;
unicode_long    = "U" , hex , hex , hex , hex , hex , hex , hex , hex ;
hex             = digit | "A"-"F" | "a"-"f" ;

(* Primitives *)
letter          = "A"-"Z" | "a"-"z" ;
digit           = "0"-"9" ;
digit_nonzero   = "1"-"9" ;
whitespace      = " " | TAB ;
any_char        = ? any UTF-8 character except newline ? ;
url             = ? valid URL per RFC 3986 ? ;
expression      = ? expression as defined in ODIN Schema ? ;
```

---

## Type Reference

### Core Types

| Type | Prefix | Format | Examples |
|------|--------|--------|----------|
| String | *(none)* | Quoted | `"Honda"`, `"Price = $50"` |
| Boolean | `?` | Prefix required | `?true`, `?false` |
| Null | `~` | Literal | `~` |
| Reference | `@` | Path | `@parties[0]`, `@vehicles[0].garaging` |
| Binary | `^` | Base64 | `^SGVsbG8=`, `^sha256:ABC123...` |
| Verb | `%` | Expression | `%upper @name`, `%concat @first " " @last` |

### Numeric Types

| Type | Prefix | Description | Examples |
|------|--------|-------------|----------|
| Number | `#` | Any numeric (integer or decimal) | `#2022`, `#-45.50`, `#1.2e10` |
| Integer | `##` | Whole number only | `##42`, `##-100`, `##0` |
| Currency | `#$` | Money (2 decimal places default) | `#$100.00`, `#$-50.25`, `#$99.99:USD` |
| Percent | `#%` | Percentage as decimal (0-1 range) | `#%0.15`, `#%1.0`, `#%0.055` |

**Numeric Rules:**
- `#` accepts any numeric precision—use for general numbers
- `##` explicitly marks value as integer; decimal values are invalid
- `#$` marks value as currency with 2 decimal places
- `#%` marks value as a percentage (stored as 0-1 decimal, where 0.5 = 50%)
- Exponential notation (`1.2e10`) valid with `#` prefix
- Negative values: place `-` after prefix (`#-45`, `##-100`, `#$-50.00`)

**Currency Code Suffix:**

Currency values can include an optional currency code suffix using colon notation:

```odin
price = #$99.99:USD     ; US Dollars
cost = #$1234.56:EUR    ; Euros
amount = #$50.00:GBP    ; British Pounds
local = #$100.00        ; No code = local currency assumed
```

When no currency code is specified, local currency is assumed based on context.

**Schema-Only Numeric Types:**

The following numeric type prefixes are used in ODIN Schema to declare field constraints, but do not appear in data values:

| Schema Type | Meaning | Data Representation |
|-------------|---------|---------------------|
| `#.N` | Decimal with exactly N places | Use `#` with appropriate precision |
| `#$.N` | Currency with N decimal places | Use `#$` with appropriate precision |

In schema: `rate = #.4:(0..1)` declares a 4-decimal field.
In data: `rate = #0.0500` just uses the `#` prefix with the value.

### Temporal Types

| Type | Prefix | Format | Examples |
|------|--------|--------|----------|
| Date | *(none)* | `YYYY-MM-DD` | `2024-06-15` |
| Timestamp | *(none)* | ISO 8601 | `2025-12-06T14:30:00Z` |
| Time | `T` | `THH:MM:SS[.sss]` | `T14:30:00`, `T09:00:00.500` |
| Duration | `P` | ISO 8601 duration | `P6M`, `P1Y`, `PT30M`, `P1DT12H` |

**Temporal Rules:**
- Date and timestamp are distinguished by presence of `T` and time component
- Time values always start with `T` prefix
- Duration values always start with `P` prefix
- Timestamps should include timezone (`Z` or `±HH:MM`)

---

## Modifier Reference

| Modifier | Symbol | Position | Meaning |
|----------|--------|----------|---------|
| Critical | `!` | After `=` | Field is required; absence fails validation |
| Confidential | `*` | After `!` or `=` | Value contains sensitive data; downstream systems should handle accordingly |
| Deprecated | `-` | After `=` | Field is obsolete; may be removed in future |

**Modifier Order:** `= [!][-][*][type_prefix]value`

```odin
field = !"value"         ; critical string
field = *"value"         ; confidential string
field = -"value"         ; deprecated string
field = !*"value"        ; critical + confidential string
field = !-"value"        ; critical + deprecated (still required but obsolete)
field = -*"value"        ; deprecated + confidential
field = !-*"value"       ; critical + deprecated + confidential
field = !#100            ; critical number
field = !##42            ; critical integer
field = !#$99.99         ; critical currency
field = !*#100           ; critical + confidential number
field = !@path           ; critical reference
field = !?true           ; critical boolean
```

**Modifier Semantics:**
- **Critical (`!`)** — Field must be present and non-null; validation fails if absent
- **Confidential (`*`)** — Value contains sensitive data; the actual value is stored in the document, but the modifier signals to downstream systems (loggers, displays, APIs, AI pipelines) that the field should be treated as sensitive. The `*` modifier does not mask or alter the value — it is metadata that travels with the data.
- **Deprecated (`-`)** — Field exists for backward compatibility; consumers should migrate to alternatives

---

## Path Reference

| Element | Syntax | Example |
|---------|--------|---------|
| Simple path | `segment.segment` | `policy.effective` |
| Array access | `segment[n]` | `vehicles[0]` |
| Nested array | `segment[n].segment[n]` | `drivers[0].violations[2]` |
| Extension | `&domain.path` | `&com.acme.custom_field` |

### Extension Paths

Extensions use reverse domain notation to namespace custom fields:

```odin
&com.progressive.symbol = #15
&com.safeco.tier = "A"
&org.example.custom = "value"
```

---

## Headers

Headers set a path prefix for subsequent assignments.

| Syntax | Meaning | Example |
|--------|---------|---------|
| `{path}` | Set absolute prefix | `{vehicles[0]}` |
| `{.path}` | Set relative prefix | `{.garaging}` |
| `{}` | Reset to root | `{}` |

### Relative Headers

```odin
{vehicles[0]}
vin = "1HGCM82633A004352"
year = #2022

{.garaging}                    ; resolves to vehicles[0].garaging
line1 = "123 Main Street"
city = "Columbus"

{.lienholder}                  ; resolves to vehicles[0].lienholder
name = "First National Bank"

{drivers[0]}                   ; absolute, resets context
name.first = "John"
```

---

## Document Metadata

The `$` path is reserved for document-level metadata:

```odin
{$}
odin = "1.0.0"
id = "doc_abc123"
created = 2025-12-06T14:30:00Z
source.format = "al3"
source.version = "2024.1"
hash = ^sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
signature = ^ed25519:SGVsbG8gV29ybGQhIFRoaXMgaXMgYSB0ZXN0Lg==
```

### Reserved Metadata Fields

| Field | Type | Description |
|-------|------|-------------|
| `$.odin` | String | ODIN version |
| `$.id` | String | Document identifier |
| `$.created` | Timestamp | Creation time |
| `$.source.format` | String | Original format |
| `$.source.version` | String | Source format version |
| `$.hash` | Binary | Document hash |
| `$.signature` | Binary | Digital signature |

---

## Document Chaining

ODIN supports composing multiple documents into a single stream or referencing external documents. This enables layered data models where a base document is modified by subsequent documents.

### Document Separator

The `---` separator divides multiple ODIN documents within a single stream:

```odin
{$}
odin = "1.0.0"
id = "insured_001"
role = "header"

{insured}
name.first = "John"
name.last = "Smith"
dob = 1985-03-15
ssn = *"987-65-4321"

---

{$}
odin = "1.0.0"
id = "policy_base_001"
role = "base"
parent = @insured_001

{policy}
number = "PAP-2024-001"
effective = 2024-06-15
term = P6M

{vehicles[0]}
vin = "1HGCM82633A004352"
year = #2022
make = "Honda"
model = "Accord"

---

{$}
odin = "1.0.0"
id = "endorsement_001"
role = "endorsement"
parent = @policy_base_001
effective = 2024-09-01

{vehicles[1]}
vin = "5YJSA1E26MF123456"
year = #2023
make = "Tesla"
model = "Model 3"
```

### Document Roles

| Role | Purpose |
|------|---------|
| `header` | Shared entity data (insured, applicant) that persists across transactions |
| `base` | Original policy or transaction |
| `endorsement` | Modification to base document |
| `renewal` | New term based on prior policy |
| `cancellation` | Terminates a policy |
| `reinstatement` | Restores a cancelled policy |

### Import Directive

The `@import` directive includes external ODIN documents:

```odin
@import ./insured.odin as insured
@import ./policy_base.odin as base
@import https://schemas.example.com/auto/2024.odin

{$}
odin = "1.0.0"
id = "endorsement_002"
role = "endorsement"
parent = @base

{drivers[1]}
name.first = "Jane"
name.last = "Smith"
license.number = "S123-456-789"
license.state = "TX"
```

### Import Resolution

| Syntax | Resolution |
|--------|------------|
| `./path.odin` | Relative to current document |
| `../path.odin` | Parent directory relative |
| `/path.odin` | Absolute filesystem path |
| `https://...` | Remote URL (cached locally) |

### Alias Usage

When imported with `as alias`, the document's content is available under that namespace:

```odin
@import ./shared/address_types.odin as addr

{customer.billing}
= @addr.us_address              ; references type from imported document
```

### Schema Directive

The `@schema` directive declares the schema for validation:

```odin
@schema https://odin.example.org/schemas/auto/policy/1.0.odin

{$}
odin = "1.0.0"
id = "policy_001"

{policy}
number = "PAP-2024-001"
; ... validated against referenced schema
```

### Conditional Directive

The `@if` directive enables conditional inclusion of subsequent assignments:

```odin
@if policy.state = "TX"
{endorsement}
sr22 = ?true
filing_date = 2024-06-15

@if policy.state = "CA"
{endorsement}
proof_of_insurance = ?true
```

**Implementation Note:** The parser extracts `@if` conditions and stores them as metadata on the document. Condition evaluation is delegated to the application layer, which has access to the full context needed for evaluation. This design provides flexibility for different evaluation strategies (compile-time, runtime, external context) and allows consuming systems to evaluate conditions based on their specific requirements.

### Chaining Semantics

When documents are chained:

1. **Overlay model** — Later documents overlay earlier documents
2. **Path precedence** — Same path in later document replaces earlier value
3. **Array handling** — Arrays merge by index; explicit `field[] = ~` clears array
4. **Null removes** — `field = ~` in later document removes field from result
5. **Metadata isolation** — Each document's `$` header is independent

### Current State Computation

To compute current state from a chain:

```
result = {}
for each document in chain:
    for each assignment in document:
        if value == ~:
            delete result[path]
        else:
            result[path] = value
return result
```

### Chain Metadata

Documents can declare their position in a chain:

```odin
{$}
odin = "1.0.0"
id = "endorsement_003"
role = "endorsement"
parent = @policy_base_001         ; references parent document
effective = 2024-09-15            ; when this change takes effect
supersedes = @endorsement_002     ; replaces a prior endorsement
sequence = #3                     ; explicit ordering
```

---

## Strings

All string values in ODIN must be quoted with double quotes (`"`).

### Quoted Strings

Double-quoted strings support escape sequences for special characters:

```odin
make = "Honda"
city = "New York"
name = "John Smith III"
description = "Price = $50; includes tax"
note = "Line one\nLine two"
empty = ""
path = "C:\\Users\\John"
quote = "He said \"hello\""
emoji = "\u263A"
```

### Escape Sequences

| Sequence | Character |
|----------|-----------|
| `\\` | Backslash |
| `\"` | Double quote |
| `\n` | Newline (U+000A) |
| `\t` | Tab (U+0009) |
| `\r` | Carriage return (U+000D) |
| `\0` | Null (U+0000) |
| `\uXXXX` | Unicode code point (4 hex digits) |
| `\UXXXXXXXX` | Unicode code point (8 hex digits) |

### String Rules

1. **All strings must be quoted** - `field = "value"`
2. **Empty string** - Use empty quotes: `field = ""`
3. **Special characters** - Escaped within quotes: `field = "a = b"`
4. **Whitespace** - Preserved exactly as written inside quotes

---

## Validation Rules

### Structural Rules

1. **Contiguous arrays** - Array indices must be sequential starting from 0
2. **Unique paths** - Each fully-qualified path may only be assigned once per document
3. **Forward references allowed** - References may point to paths defined later
4. **No circular references** - Reference chains must not form cycles
5. **Valid references** - All references must resolve to defined paths
6. **Import resolution** - All imports must resolve to valid ODIN documents

### Value Rules

1. **Strings must be quoted** - All string values require double quotes: `field = "value"`
2. **Empty string** - Use empty quotes: `field = ""`
3. **Null vs absent** - `field = ~` is explicit null; absent field is undefined
4. **Null array** - `field[] = ~` indicates explicit empty array
5. **Whitespace** - Preserved exactly as written inside quotes

### Implementation Limits

Implementations should support at minimum:

| Limit | Minimum Required |
|-------|------------------|
| Path depth | 64 levels |
| Path segment length | 256 characters |
| String length | 1 MB |
| Number precision | IEEE 754 double (64-bit) |
| Integer range | ±2^53 (safe JavaScript integer) |
| Array length | 10,000 elements |
| Document size | 100 MB |

Implementations may support larger values. Documents exceeding these limits are valid ODIN but may not be portable across all implementations.

### Number Precision

- `#` (generic number): IEEE 754 double precision; integers up to 2^53 are exact
- `##` (integer): Must be whole number; implementations may use 64-bit signed integer
- `#$` (currency): Two decimal places; implementations should use decimal arithmetic to avoid floating-point errors

For financial applications requiring exact decimal arithmetic, implementations should use decimal types internally, not binary floating-point.

### Error Handling

Parsers encountering invalid input should:

1. **Fail fast** — Report the first error with line number and position
2. **Be specific** — Identify what was expected vs. what was found
3. **No recovery** — Do not attempt to parse past errors; ODIN has no error recovery mode

Valid error conditions include:
- Invalid UTF-8 byte sequence
- Unrecognized escape sequence in quoted string
- Non-contiguous array indices
- Duplicate path assignment
- Circular reference detected
- Invalid numeric format
- Malformed date/timestamp

---

## Canonical Form

For deterministic output, ODIN documents should be written in canonical form:

1. **Header `$` first** - Document metadata at top
2. **Sorted paths** - Assignments sorted lexicographically by full path
3. **No blank lines** - Remove aesthetic blank lines
4. **No comments** - Remove comments
5. **Minimal whitespace** - Single space around `=`
6. **LF line endings** - Unix-style
7. **Quoted strings** - All strings use double quotes
8. **Minimal escaping** - In quoted strings, only escape characters that require it
9. **Imports before content** - All import directives before document metadata
10. **Single document** - Canonical form applies per document in a chain

Canonical form ensures identical documents produce identical byte sequences.

### Canonical Sorting Algorithm

Paths are sorted using the following rules:

1. **Byte-wise comparison** — Compare UTF-8 bytes, not Unicode code points
2. **Segment-by-segment** — Split path on `.` and compare segments left to right
3. **Array indices numeric** — `[2]` sorts before `[10]` (numeric, not lexicographic)
4. **`$` metadata first** — `$` path prefix sorts before all other paths
5. **Extensions last** — `&domain.field` paths sort after non-extension paths

**Sorting Examples:**

```
$.odin
$.id
customer.address.city
customer.address.zip
customer.name
items[0].name
items[0].price
items[1].name
items[1].price
items[10].name
&com.acme.custom
```

### Canonical String Representation

All strings are quoted:

| Content | Example |
|---------|---------|
| Simple text | `"Honda"` |
| Contains special chars | `"a = b"` |
| Contains `"` | `"say \"hi\""` |
| Contains newline | `"line1\nline2"` |
| Empty | `""` |
| With whitespace | `"  padded  "` |

### Canonical Number Representation

1. **No leading zeros** — `#42` not `#042`
2. **No trailing zeros in decimals** — `#3.14` not `#3.140` (unless currency)
3. **Currency always two decimals** — `#$10.00` not `#$10`
4. **Currency code uppercase** — `#$10.00:USD` not `#$10.00:usd`
5. **Percent as decimal** — `#%0.15` for 15%, not `#%15`
6. **No positive sign** — `#42` not `#+42`
7. **Lowercase exponent** — `#1.2e10` not `#1.2E10`

---

## JSON Conversion

ODIN converts losslessly to and from JSON.

### ODIN to JSON Example

**ODIN:**

```odin
{policy}
id = "PAP-123"
effective = 2024-06-15
term = P6M
premium.total = #747.50
active = ?true
notes = ~
description = "Coverage for vehicle; standard terms"
```

**JSON:**

```json
{
  "policy": {
    "id": "PAP-123",
    "effective": "2024-06-15",
    "term": "P6M",
    "premium": {
      "total": 747.50
    },
    "active": true,
    "notes": null,
    "description": "Coverage for vehicle; standard terms"
  }
}
```

### Type Mapping

| ODIN | JSON |
|------|------|
| String | string |
| Number (`#`) | number |
| Integer (`##`) | number (integer) |
| Currency (`#$`) | number |
| Percent (`#%`) | number |
| Boolean | boolean |
| Null | null |
| Date | string (ISO 8601) |
| Timestamp | string (ISO 8601) |
| Time | string (`T` prefix preserved) |
| Duration | string (`P` prefix preserved) |
| Reference | string (`@` prefix preserved) |
| Binary | string (with algorithm prefix if present) |
| Array | array |
| Object | object |

**Numeric Type Preservation:**

When converting to JSON, numeric type metadata can be preserved via sibling fields:

**ODIN:**
```odin
price = #$99.99
count = ##42
rate = #0.0525
```

**JSON (with metadata):**
```json
{
  "price": 99.99,
  "price$type": "currency",
  "count": 42,
  "count$type": "integer",
  "rate": 0.0525
}
```

**JSON (simple, lossy):**
```json
{
  "price": 99.99,
  "count": 42,
  "rate": 0.0525
}
```

### Modifiers in JSON

Critical, confidential, and deprecated modifiers are preserved via sibling metadata:

**ODIN:**

```odin
vin = !*"ABC123"
legacy_code = -"OLD001"
old_secret = !-*"legacy_value"
```

**JSON:**

```json
{
  "vin": "ABC123",
  "vin$critical": true,
  "vin$confidential": true,
  "legacy_code": "OLD001",
  "legacy_code$deprecated": true,
  "old_secret": "legacy_value",
  "old_secret$critical": true,
  "old_secret$deprecated": true,
  "old_secret$confidential": true
}
```

### Chained Documents in JSON

Multiple documents serialize as a JSON array:

**ODIN:**

```odin
{$}
id = "doc1"
---
{$}
id = "doc2"
```

**JSON:**

```json
[
  { "$": { "id": "doc1" } },
  { "$": { "id": "doc2" } }
]
```

---

## Examples

### Minimal Document

```odin
{$}
odin = "1.0.0"

{order}
id = "ORD-12345"
total = #99.99
```

### All Types Example

```odin
{$}
odin = "1.0.0"
id = "example-all-types"
created = 2025-12-06T14:30:00Z

{record}
; Strings
string_simple = "Hello World"
string_special = "Contains = and ; special chars"
string_escape = "Line one\nLine two"
string_unicode = "Smiley: \u263A"
string_empty = ""

; Booleans
bool_true = ?true
bool_false = ?false

; Numbers - generic
number_int = #42
number_dec = #3.14159
number_neg = #-273.15
number_exp = #6.022e23

; Numbers - typed
integer_val = ##42
integer_neg = ##-100
currency_val = #$199.99
currency_neg = #$-50.00

; Null
null_val = ~

; Temporal
date_val = 2024-06-15
timestamp_val = 2025-12-06T14:30:00Z
timestamp_offset = 2025-12-06T09:30:00-05:00
time_val = T09:30:00
time_millis = T09:30:00.500
duration_val = P1Y6M
duration_time = PT2H30M

; References
reference_val = @other[0]
reference_nested = @other[0].details

; Binary
binary_val = ^SGVsbG8gV29ybGQh
binary_hash = ^sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

; Modifiers
critical_val = !"required"
redacted_val = *"123-45-6789"
deprecated_val = -"old_value"
critical_redacted = !*"secret_value"
critical_deprecated = !-"obsolete_but_required"
deprecated_redacted = -*"legacy_secret"
all_modifiers = !-*"secret_legacy_required"
critical_number = !#100.00
critical_integer = !##42
critical_currency = !#$99.99
redacted_currency = *#$50.00

{other[0]}
id = "ref-target"
details.info = "nested reference target"
```

### Extensions Example

```odin
{$}
odin = "1.0.0"

{item}
id = "ITEM-001"
name = "Standard Widget"
price = #29.99
&com.acme.warehouse = "B7"
&com.acme.shelf = #14
&org.partner.sku = "WIDGET-A"
```

### Chained Policy Example

```odin
; Header document - insured entity
{$}
odin = "1.0.0"
id = "insured_smith_john"
role = "header"

{insured}
name.first = "John"
name.last = "Smith"
dob = 1985-03-15
ssn = !*"123-45-6789"

{insured.address}
line1 = "123 Main Street"
city = "Austin"
state = "TX"
zip = "78701"

---

; Base policy document
{$}
odin = "1.0.0"
id = "policy_PAP2024001"
role = "base"
parent = @insured_smith_john
effective = 2024-06-15

{policy}
number = "PAP-2024-001"
term = P6M
type = "personal_auto"

{vehicles[0]}
vin = "1HGCM82633A004352"
year = #2022
make = "Honda"
model = "Accord"
use = "commute"

{drivers[0]}
relation = "insured"
license.number = "12345678"
license.state = "TX"

{coverages.liability}
limit = #100000
deductible = #500

{premium}
total = #747.50
method = "monthly"

---

; Endorsement - add vehicle
{$}
odin = "1.0.0"
id = "endorsement_001"
role = "endorsement"
parent = @policy_PAP2024001
effective = 2024-09-01
sequence = #1

{vehicles[1]}
vin = "5YJSA1E26MF123456"
year = #2023
make = "Tesla"
model = "Model 3"
use = "pleasure"

{premium}
total = #892.00              ; updated premium

---

; Endorsement - SR-22 filing
{$}
odin = "1.0.0"
id = "endorsement_002"
role = "endorsement"
parent = @policy_PAP2024001
effective = 2024-09-15
sequence = #2

{endorsements.sr22}
required = ?true
state = "TX"
filing_date = 2024-09-15
reason = "reinstatement"

{premium}
total = #945.00              ; updated with SR-22 surcharge
```

### Complete Single Document Example

```odin
; ODIN Document - Sample Order
; Generated: 2025-12-06

{$}
odin = "1.0.0"
id = "doc_abc123"
created = 2025-12-06T14:30:00Z
source.format = "json"
source.version = "1.0"

{order}
id = "ORD-2024-12345"
status = "confirmed"
placed = 2025-12-06T10:30:00Z
total = #299.97
notes = "Customer requested gift wrap; include receipt"

{customer}
id = "cust_001"
name.first = "Jane"
name.last = "Doe"
email = !"jane.doe@email.com"
phone = *"555-867-5309"
member_since = 2020-03-15

{customer.address}
line1 = "456 Oak Avenue"
line2 = "Apt 7B"
city = "Austin"
state = "TX"
zip = "78701"
country = "US"

{items[0]}
sku = "WIDGET-A"
name = "Premium Widget"
description = "High-quality widget; rated #1 in class"
quantity = #2
price = #49.99
subtotal = #99.98

{items[1]}
sku = "GADGET-B"
name = "Deluxe Gadget"
description = "Features: A, B, and C"
quantity = #1
price = #199.99
subtotal = #199.99

{shipping}
method = "express"
carrier = @carriers[0]
estimated = 2025-12-09
cost = #0.00
instructions = "Leave at door\nDo not ring bell"

{carriers[0]}
id = "carrier_ups"
name = "UPS"
tracking = "1Z999AA10123456784"

{discounts[0]}
code = "HOLIDAY20"
type = "percentage"
value = #20
applied = #-59.99

{payment}
method = "credit_card"
last_four = *"1234"
authorized = ?true
transaction = "txn_abc123xyz"
```

---

## Future Considerations

The following features are under consideration for future versions. Parsers should not implement these until formally specified.

### Formal Test Suite

The ODIN Golden Test Suite is available at [github.com/odin-foundation/odin-golden-tests](https://github.com/odin-foundation/odin-golden-tests). It provides 159 cross-language test files covering parsing, serialization, canonicalization, schema validation, transforms, and end-to-end export. All SDK implementations must pass these tests with identical behavior.

### Conformance Levels (v1.x)

Defining strict vs. lenient parsing modes:

- **Strict:** Reject any deviation from spec
- **Lenient:** Accept common variations (e.g., Windows line endings, relaxed whitespace)
- **Canonical:** Output-only mode producing deterministic form

**Assessment:** Useful for interoperability. Defer to v1.1.

### Unicode Identifiers (v2)

Allowing non-ASCII characters in field names:

```odin
; Possible future syntax - NOT YET SPECIFIED
prénom = "Jean"
名前 = "田中"
```

**Assessment:** Complex implications for canonical sorting, tooling support. Defer to v2.

### Binary Encoding (v2)

A compact binary representation of ODIN for performance-critical applications:

- Same logical model as text ODIN
- Lossless round-trip to text form
- Optimized for size and parse speed

**Assessment:** Useful for high-volume scenarios. Needs careful design. Defer to v2.

### Streaming Parser API (v2)

Event-based parsing for large documents:

- SAX-style callbacks
- Memory-efficient processing
- Partial document handling

**Assessment:** Useful for large files. Implementation detail more than spec change. Defer to v2.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12 | Initial specification |

---

**End of Specification**
