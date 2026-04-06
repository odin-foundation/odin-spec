# ODIN Schema 1.0: Reference

<!-- LLM:
PURPOSE: Derivation, security, validation rules, complete example, EBNF grammar
DERIVATION: {$derivation} with source[n].authority/citation/url, methodology, changelog
SECURITY: Pattern ReDoS limits (500 chars, 100ms, 10K backtrack), schema injection mitigation
VALIDATION: Field (!required, ~nullable, *confidential, -deprecated), type, constraint, conditional, invariant, cardinality
JSON_MAPPING: !=required[], ~=type+null, ##=integer, :(bounds)=min/max, :/regex/=pattern
SEE ALSO: Foundations.md Types.md Composition.md Advanced.md
-->

---

## Schema Derivation

The `$derivation` header documents the provenance of schema definitions.

### Purpose

1. **Legal protection** — Documents independent derivation from public sources
2. **Auditability** — Traces each schema to authoritative requirements
3. **Compliance** — Proves adherence to regulatory mandates
4. **Transparency** — Makes design decisions visible and reviewable

### Derivation Header Structure

```odin
{$derivation}
source[0].authority = "Texas Department of Insurance"
source[0].citation = "TX Admin Code Title 28, Part 1, Chapter 5"
source[0].url = "https://texreg.sos.state.tx.us/..."
source[0].accessed = 2025-11-15

source[1].authority = "California Department of Insurance"
source[1].citation = "CA Code of Regulations Title 10, Chapter 5"
source[1].accessed = 2025-11-15

methodology = "regulatory_derivation"
proprietary_sources_consulted = ?false

changelog[0].date = 2025-11-20
changelog[0].change = "Initial schema derived from TX/CA/NAIC requirements"
changelog[0].rationale = "Coverage requirements defined in state filing mandates"
```

### Derivation Metadata Fields

| Field | Type | Description |
|-------|------|-------------|
| `source[n].authority` | String | Regulatory body or public source |
| `source[n].citation` | String | Specific regulation or document |
| `source[n].url` | String | URL to public source |
| `source[n].accessed` | Date | When source was accessed |
| `methodology` | String | Derivation approach used |
| `proprietary_sources_consulted` | Boolean | Whether proprietary standards were referenced |
| `changelog[n].date` | Date | When change was made |
| `changelog[n].change` | String | What changed |
| `changelog[n].rationale` | String | Why the change was made |

### Methodology Values

| Value | Meaning |
|-------|---------|
| `regulatory_derivation` | Derived from government regulatory requirements |
| `industry_practice` | Based on common industry practices |
| `domain_analysis` | Original design based on domain requirements |
| `extension` | Extends an existing ODIN schema |

---

## Security Considerations

### Pattern (Regex) Security

Regular expressions in `:/.../` constraints can be dangerous:

**ReDoS Prevention:**

Catastrophic backtracking can cause denial of service. Avoid:
- Nested quantifiers: `(a+)+`
- Overlapping alternations: `(a|a)+`
- Unbounded repetition with overlap: `.*.*`

**Recommended Limits:**
- Pattern length: 500 characters maximum
- Execution time: 100ms timeout per match
- Backtrack limit: 10,000 steps maximum

**Safe Pattern Examples:**
```odin
; Safe - anchored, bounded, no nested quantifiers
vin = :/^[A-HJ-NPR-Z0-9]{17}$/
email = :/^[^@]+@[^@]+\.[^@]+$/
phone = :/^\d{3}-\d{3}-\d{4}$/
```

**Unsafe Pattern Examples:**
```odin
; UNSAFE - nested quantifiers, catastrophic backtracking risk
bad = :/^(a+)+$/
bad = :/^([a-zA-Z]+)*$/
```

### Schema Injection

Schemas loaded from untrusted sources could contain:

1. **Malicious patterns** — ReDoS patterns to cause CPU exhaustion
2. **Excessive constraints** — Very large enum lists or deep nesting
3. **Import chains** — Circular or deeply nested imports

**Mitigations:**
- Validate schemas before use
- Limit import depth (recommend: 10 levels maximum)
- Limit enum size (recommend: 1,000 values maximum)

### Memory Safety

**Recommended Limits:**
- Type definitions: 1,000 maximum per schema
- Array bounds: 100,000 elements maximum
- String length bounds: 10MB maximum

---

## Validation Rules

### Field Validation

1. **Required fields** (`!`) must be present and non-null
2. **Nullable fields** (`~`) may be null or absent
3. **Optional fields** (no modifier) may be absent but not null
4. **Deprecated fields** (`-`) generate warnings, still validated
5. **Confidential fields** (`*`) are validated normally; the modifier signals sensitivity to downstream consumers

### Type Validation

| Type | Requirements |
|------|-------------|
| String | UTF-8 text, length constraints apply to characters |
| Boolean | Must be `?true` or `?false` |
| Integer | Whole number, no decimal point |
| Decimal | Fixed precision enforced |
| Currency | Fixed 2 decimals unless specified |
| Date | ISO 8601 date format |
| Timestamp | ISO 8601 timestamp with timezone |
| Time | ISO 8601 time with `T` prefix |
| Duration | ISO 8601 duration with `P` prefix |
| Reference | Must resolve to valid path |
| Binary | Valid base64, algorithm prefix if specified |
| Union | First matching type wins (declaration order) |

### Constraint Validation

1. **Bounds** - Value must be within specified range
2. **Pattern** - Value must match regular expression
3. **Enum** - Value must be one of listed options
4. **Unique** - Array elements must be distinct

### Conditional Validation

1. `:if field = value` is true when field equals value
2. `!:if` means required when condition is true, ignored otherwise
3. Multiple `:if` lines on same field act as OR
4. If discriminator field is absent, all its conditionals are ignored

### Invariant Validation

1. Invariants apply to fields within their header's scope
2. Evaluated after all field validations pass
3. Circular dependencies between invariants are schema errors
4. Any null operand makes expression evaluate to false

### Cardinality Validation

1. `:of` constraints apply to fields within their header's scope
2. A field "counts" if present and non-null
3. `(1..)` = at least 1, `(..1)` = at most 1, `(2..3)` = 2 to 3

---

## Complete Example

```odin
{$}
odin = "1.0.0"
schema = "1.0.0"
id = "org.odin.ecommerce.order"
version = "1.0.0"

{$derivation}
methodology = "domain_analysis"
proprietary_sources_consulted = ?false
changelog[0].date = 2025-12-01
changelog[0].change = "Initial order schema"

{@address}
line1 = !:(1..100)
line2 = :(..100)
city = !:(1..50)
state = !:(2)
zip = !:/^\d{5}(-\d{4})?$/
country = :(2) "US"

{@money}
amount = !#$:(0..999999.99)
currency = :(3) "USD"

{order}
id = !:/^ORD-\d{8}$/
status = !(pending, confirmed, shipped, delivered, cancelled)
placed = !timestamp
notes = ~:(..1000)

{customer}
id = !:/^CUST-\d+$/
name.first = !:(1..50)
name.last = !:(1..50)
email = !:/^[^@]+@[^@]+\.[^@]+$/
phone = :/^\d{3}-\d{3}-\d{4}$/
ssn = *:/^\d{3}-\d{2}-\d{4}$/
:one_of email, phone

{customer.billing}
= @address

{items[]}
:(1..100)
sku = !:/^[A-Z]+-[A-Z0-9]+$/
name = !:(1..200)
quantity = !##:(1..999)
unit_price = !#$:(0..)
subtotal = !#$:(0..)
:invariant subtotal = quantity * unit_price

{totals}
subtotal = !@money
tax = !@money
shipping = !@money
discount = @money
total = !@money
:invariant total.amount >= 0

{payment}
method = !(credit_card, debit_card, paypal, bank_transfer)
transaction_id = !:(10..50)
card_number = !*:(16):if method = credit_card
card_number = !*:(16):if method = debit_card
card_expiry = !:(5):if method = credit_card
card_expiry = !:(5):if method = debit_card
paypal_email = !:/^[^@]+@[^@]+$/:if method = paypal
bank_routing = !:(9):if method = bank_transfer
bank_account = !*:(..17):if method = bank_transfer
```

---

## Formal Grammar (EBNF)

```ebnf
(* Schema Document *)
schema_doc      = { line } ;
line            = ( comment | header | definition | object_constraint | blank ) , EOL ;

(* Headers *)
header          = "{" , header_type , "}" ;
header_type     = metadata_header | derivation_header | type_def_header | array_header | tabular_array_header | path_header ;
metadata_header = "$" ;
derivation_header = "$derivation" ;
type_def_header = "@" , [ namespace ] , identifier ;
namespace       = "&" , reverse_dns , "." ;
reverse_dns     = domain_part , { "." , domain_part } ;
domain_part     = letter , { letter | digit | "-" } ;
array_header    = path , "[" , "]" ;
tabular_array_header = path , "[" , "]" , ":" , column_list ;
column_list     = identifier , { "," , identifier } ;
path_header     = path ;

(* Field Definition *)
definition      = path , "=" , [ modifiers ] , [ type_spec ] , [ constraint ] , [ conditional ] , [ default ]
                | "=" , intersection ;

(* Object-Level Constraints *)
object_constraint = invariant | cardinality ;
invariant       = ":invariant" , expression ;
cardinality     = cardinality_kw , field_list ;
cardinality_kw  = ":of" , bounds | ":one_of" | ":exactly_one" | ":at_most_one" ;
field_list      = identifier , { "," , identifier } ;

(* Expressions for Invariants *)
expression      = logic_or ;
logic_or        = logic_and , { "||" , logic_and } ;
logic_and       = equality , { "&&" , equality } ;
equality        = comparison , { ( "==" | "!=" ) , comparison } ;
comparison      = additive , { ( ">" | "<" | ">=" | "<=" ) , additive } ;
additive        = multiplicative , { ( "+" | "-" ) , multiplicative } ;
multiplicative  = unary , { ( "*" | "/" | "%" ) , unary } ;
unary           = [ "!" ] , primary ;
primary         = path | number | "(" , expression , ")" ;

(* Modifiers *)
modifiers       = { modifier } ;
modifier        = "!" | "~" | "*" | "-" ;

(* Type Specifiers *)
type_spec       = union_type | single_type ;
union_type      = single_type , { "|" , union_member } ;
union_member    = single_type | "~" | '""' ;
single_type     = bool_type | numeric_type | temporal_type | reference_type | binary_type | enum_type | type_ref ;

bool_type       = "?" ;
numeric_type    = "#" | "##" | "#" , "." , digits | "#$" , [ "." , digits ] | "#%" ;
temporal_type   = "date" | "timestamp" | "time" | "duration" ;
reference_type  = "@" , [ path ] ;
binary_type     = "^" , [ algorithm ] ;
enum_type       = "(" , enum_values , ")" ;
type_ref        = "@" , [ namespace ] , identifier ;

(* Intersection *)
intersection    = type_ref , { "&" , type_ref } , [ ":override" ] ;

(* Constraints *)
constraint      = ":" , constraint_expr ;
constraint_expr = bounds | pattern | "unique" | algorithm_constraint | format_constraint ;
bounds          = "(" , [ bound_min ] , ".." , [ bound_max ] , ")" ;
pattern         = delimiter , regex , delimiter ;
format_constraint = "format" , format_name ;

(* Conditionals *)
conditional     = ":if" , path , operator , value | ":unless" , path , operator , value ;
operator        = "=" | "!=" | ">" | "<" | ">=" | "<=" ;

(* Primitives *)
identifier      = letter , { letter | digit | "_" } ;
path            = identifier , { "." , identifier } ;
algorithm       = letter , { letter | digit | "-" } ;
digits          = digit , { digit } ;
number          = [ "-" ] , digits , [ "." , digits ] ;
typed_value     = (* as defined in ODIN 1.0 *) ;
```

---

## JSON Schema Mapping

| ODIN Schema | JSON Schema |
|-------------|-------------|
| `!` | `"required": ["field"]` |
| `~` (prefix) | `"type": [..., "null"]` |
| `?` | `"type": "boolean"` |
| `#` | `"type": "number"` |
| `##` | `"type": "integer"` |
| `#.2` | `"type": "number", "multipleOf": 0.01` |
| `#$` | `"type": "number", "multipleOf": 0.01` |
| `:(min..max)` | `"minLength"/"maxLength"` or `"minimum"/"maximum"` |
| `:/regex/` | `"pattern": "regex"` |
| `(a, b, c)` | `"enum": ["a", "b", "c"]` |
| `#\|""` | `"type": ["number", "string"]` |
| `#\|~` | `"oneOf": [{"type": "number"}, {"type": "null"}]` |
| `[]` | `"type": "array"` |
| `@path` | `"$ref": "#/path"` |
| `@a & @b` | `"allOf": [{"$ref": "#/a"}, {"$ref": "#/b"}]` |
| `:if field = value` | `"if"/"then"` conditional |
| `:invariant expr` | Custom validation (no direct equivalent) |

---

## Schema Imports

Referencing types from external schema files:

```odin
@import ./common.odin as common
@import ./vehicle.odin as vehicle

{policy}
address = @common.address
car = @vehicle.vehicle
```

**Implementation:** The TypeScript SDK provides full import resolution via the resolver module:

- **FileLoader** — Secure file loading with sandbox protection
- **CircularDetector** — Prevents infinite loops in import chains
- **TypeRegistry** — Namespaced type lookup supporting `@alias.typeName` syntax
- **ImportResolver** — Recursive resolution building merged type registry

**Security controls:**
- Remote URLs disabled by default
- Sandbox root enforcement prevents path traversal
- Maximum import depth limit (default: 32)
- File extension whitelist (default: `.odin` only)

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12 | Initial specification |

**v1.0 Features:**
- Core types: string, boolean, numeric (integer, decimal, currency, percent), temporal, reference, binary, null
- Union types: pipe-chained syntax (`#|?|~|""`)
- Constraints: bounds, patterns, enums, unique
- Modifiers: required, nullable, confidential, deprecated
- Arrays with bounds and unique constraints
- Reusable type definitions with namespaces
- Type intersections (`&`)
- Conditional fields (`:if`, `:unless`)
- Object-level invariants (`:invariant`)
- Cardinality constraints (`:of`, `:one_of`, `:exactly_one`, `:at_most_one`)
- Schema derivation metadata (`$derivation`)

---

*See also: [Foundations](Foundations.md) | [Types](Types.md) | [Composition](Composition.md) | [Advanced](Advanced.md)*
