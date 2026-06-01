# ODIN Transform 1.0: Modifiers

<!-- LLM:
PURPOSE: Field modifiers, segment headers, and literal block syntax
POSITIONING: :pos N :len N :leftPad "c" :rightPad "c" :truncate (fixed-width)
XML: :element :attr :ns prefix :cdata :omitEmpty
JSON: :type string|number|boolean :omitNull :omitEmpty :raw :array :object {key=@path}
STRING: :upper :lower :trim :maxLen N
CONDITIONAL: :if path :if path=value :unless path :default value :required
VALIDATION: :validate "regex" :enum v1,v2,v3 :range min..max
SEGMENTS: {segment.NAME[]} with :loop :counter :from :if :elif :else :literal
SEE ALSO: Core.md Verbs.md Formats.md Reference.md
-->

---

## Modifier Reference

### Positioning Modifiers (Fixed-Width)

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:pos` | `:pos N` | Start position (0-based) |
| `:len` | `:len N` | Field length |
| `:leftPad` | `:leftPad "c"` | Left-pad character |
| `:rightPad` | `:rightPad "c"` | Right-pad character |
| `:truncate` | `:truncate` | Truncate to length |

**Example:**

```odin
policy_number = @policy.number :pos 0 :len 15 :rightPad " "
year = @vehicle.year :pos 15 :len 4 :leftPad "0"
```

### XML Modifiers

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:element` | `:element` | Emit as XML element |
| `:attr` | `:attr` | Emit as attribute |
| `:ns` | `:ns prefix` | Namespace prefix |
| `:cdata` | `:cdata` | Wrap in CDATA |
| `:omitEmpty` | `:omitEmpty` | Omit if empty |

**Example:**

```odin
{Policy}
id = @policy.id :attr
Number = @policy.number
Description = @policy.description :cdata
Notes = @policy.notes :omitEmpty
```

### JSON Modifiers

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:type` | `:type string\|number\|boolean` | Force JSON type |
| `:omitNull` | `:omitNull` | Omit if null |
| `:omitEmpty` | `:omitEmpty` | Omit if empty string/array |
| `:raw` | `:raw` | Emit raw JSON (no escaping) |
| `:array` | `:array` | Force as single-element array |
| `:object` | `":object {key = @path, ...}"` | Inline object construction |

**Type Coercion:**

```odin
year = @vehicle.year :type number           ; "2024" → 2024
active = @is_active :type boolean           ; "true" → true
code = @zip :type string                    ; 78701 → "78701"
```

**Inline Object:**

```odin
contact = ":object {name = @insured.name, phone = @insured.phone, email = @insured.email}"
; Produces: {"name": "John", "phone": "512-555-1234", "email": "john@example.com"}

location = ":object {lat = @geo.latitude, lng = @geo.longitude}"
```

**Raw JSON:**

```odin
metadata = @document.json_metadata :raw     ; Emit pre-formatted JSON without escaping
```

### String Modifiers

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:upper` | `:upper` | Uppercase |
| `:lower` | `:lower` | Lowercase |
| `:trim` | `:trim` | Trim whitespace |
| `:maxLen` | `:maxLen N` | Maximum length |

### Conditional Modifiers

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:if` | `:if path` | Include if truthy |
| `:if` | `"@path :if other = value"` | Include if equals (quote the mapping) |
| `:unless` | `:unless path` | Include if falsy |
| `:default` | `:default value` | Fallback value |
| `:required` | `:required` | Error if missing |

**Examples:**

```odin
state = @policy.state :default "TX"
premium = @premium :required
discount = @discount :if hasDiscount
legacy_field = @old_field :unless isNewFormat
```

### Validation Modifiers

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:validate` | `:validate "regex"` | Regex validation |
| `:enum` | `:enum v1,v2,v3` | Enum validation (unquoted values; quoted `:enum "v1,v2,v3"` is also accepted) |
| `:range` | `"@path :range min..max"` | Range validation (quote the mapping) |

**Examples:**

```odin
email = @contact.email :validate "^[^@]+@[^@]+$"
status = @status :enum A,P,C
year = "@year :range 1900..2100"
policy = @policy.number :required
```

### Security Modifiers

| Modifier | Syntax | Description |
|----------|--------|-------------|
| `:confidential` | `:confidential` | Mark for confidential enforcement |

Used with `enforceConfidential` header:

```odin
{$}
enforceConfidential = "redact"

{Customer}
SSN = @.ssn :confidential     ; Confidential — enforcement nullifies on output
```

---

## Segment Headers

### Basic Segments

```odin
{segment.HDR}
; Header record fields

{segment.DET}
; Detail record fields
```

### Array Segments

```odin
{segment.VEH[]}
:loop vehicles
vin = @.vin
year = @.year
```

### Segment Directives

| Directive | Syntax | Description |
|-----------|--------|-------------|
| `_pass` | `_pass = ##N` | Execute in pass N |
| `:type` | `:type "value"` | Handle records where discriminator equals value |
| `:loop` | `:loop path` | Iterate array |
| `:loop` | `:loop path :as alias` | Iterate with alias |
| `:counter` | `:counter name` | Loop counter |
| `:from` | `:from path` | Set context |
| `:if` | `:if <verb-expr>` | Conditional segment |
| `:elif` | `:elif <verb-expr>` | Else-if branch of a chain |
| `:else` | `:else` | Else branch of a chain |
| `:literal` | `:literal` | Literal block mode |

### Conditional Segments

A segment can be emitted only when a condition holds. When the condition is
false the entire section is absent from the output — not null, omitted.

A condition is a **verb expression** evaluated for truthiness, written
unquoted; quotes are reserved for actual string operands. Use the comparison
verbs (`%eq`, `%ne`, `%gt`, `%lt`, `%gte`, `%lte`) and the boolean verbs
(`%and`, `%or`, `%not`) to compose conditions.

Two equivalent forms — header-inline and a body-line directive:

```odin
; Header-inline
{HighRisk :if %and @driver.has_dui %lt @driver.age ##25}
band = "high-risk"

; Body-line directive (identical behavior)
{HighRisk}
_if = %and @driver.has_dui %lt @driver.age ##25
band = "high-risk"
```

A legacy quoted-infix condition (`_if = "@driver.has_dui = true"`) still parses
for backward compatibility, but verb expressions are the canonical form.

### Conditional Chains (`:elif` / `:else`)

A run of consecutive segments forms a chain: one `:if`, then any number of
`:elif` branches, then an optional `:else`. Only the first branch whose
condition holds is emitted; the rest are skipped. If no condition matches, the
`:else` branch is emitted. Branches use distinct section names — exactly one of
them appears in the output.

```odin
{HighRisk :if %eq @driver.tier "dui"}
band = "high-risk"

{YoungDriver :elif %lt @driver.age ##25}
band = "young-driver"

{Standard :else}
band = "standard"
```

The chain breaks on any non-chain segment and at pass boundaries; a new `:if`
starts a fresh chain. An `:elif` or `:else` with no preceding `:if` is an error
(`T012`).

### Nested Loops

```odin
{segment.COV[]}
:loop vehicles :as veh
:loop .coverages :as cov
vehicle_vin = @veh.vin
coverage_code = @cov.code
```

### Type Discriminator

For multi-record-type files:

```odin
{$source}
format = "fixed-width"
discriminator = ":pos 0 :len 2"

{segment.HDR :type "01"}
policy.number = @. :pos 2 :len 15 :trim

{segment.VEH[] :type "20"}
vehicles[].vin = @. :pos 2 :len 17 :trim
```

---

## Literal Blocks and Interpolation

### Basic Interpolation

Inside literal blocks (`"""`), `${expression}` interpolates values:

```odin
{segment.HDR}
:literal
"""
HDR|${@policy.number}|${%formatDate @effective "YYYYMMDD"}
"""
```

### Interpolation Syntax

| Syntax | Meaning | Example |
|--------|---------|---------|
| `${@path}` | Source path value | `${@policy.number}` |
| `${@.path}` | Current loop item field | `${@.vin}` |
| `${%verb ...}` | Transformation result | `${%upper @name}` |
| `${@$accumulator.name}` | Accumulator value | `${@$accumulator.count}` |

### Escaping Rules

| Sequence | Result |
|----------|--------|
| `\${` | `${` (literal, no interpolation) |
| `\\` | `\` (literal backslash) |
| `\$` | `$` (literal dollar sign) |

**Examples:**

```odin
; To output literal ${@name}:
output = """The syntax is \${@name}"""
; Result: "The syntax is ${@name}"

; Backslash followed by interpolation:
output = """Path: \\${@path}"""
; Result: "Path: \value"
```

**No nested interpolation** — Interpolations cannot be nested:

```odin
; INVALID
value = """${@paths.${@key}}"""

; Use verb instead
value = """${%lookup PATHS @key}"""
```

---

## EBNF Grammar (Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Modifiers *)
modifier        = ":" , identifier , [ whitespace , modifier_value ] ;
modifier_value  = quoted_string | number_literal | identifier ;

(* Segment Headers *)
segment_header  = "{" , "segment." , identifier , [ "[]" ] , [ segment_opts ] , "}" ;
segment_opts    = ":type" , quoted_string ;

(* Segment Directives *)
loop_directive  = ":loop" , whitespace , path , [ " :as " , identifier ] ;
counter_directive = ":counter" , whitespace , identifier ;

(* Literal Blocks *)
literal_block   = '"""' , { literal_char | interpolation } , '"""' ;
interpolation   = "${" , ( copy_expr | transform_expr ) , "}" ;
literal_char    = "\\" , ( "$" | "\\" ) | any_char ;
```

---

*See also: [Core](Core.md) | [Verbs](Verbs.md) | [Formats](Formats.md) | [Reference](Reference.md)*
