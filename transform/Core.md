# ODIN Transform 1.0: Core

<!-- LLM:
PURPOSE: Transform document structure, mapping syntax, and fundamental concepts
CDM: ODIN is intermediate representation for all transforms (json->json = json->odin->json)
DIRECTION: Unidirectional transforms (odin->json, json->odin, xml->odin, etc.)
MAPPING: @=copy @.=current %=verb %&=custom "..."=literal ##=int #$=currency
STRUCTURE: {$} metadata, {$source} input, {$target} output, {$const} constants, {$accumulator} counters, {$table.NAME[cols]} lookups
RESOLUTION: @field resolves from SOURCE data only, never computed output. Use accumulators for chaining.
ACCUMULATORS: _ = %accumulate name value (sink field), {_step} sections, ._persist = ?true for inter-pass
LOOKUP QUOTING: TABLE.column must be quoted in verb expressions: %lookup "TABLE.column" @match
NESTING: Variadic verbs (%lookup) cannot nest inside fixed-arity verbs (%multiply) — use accumulator steps
SEE ALSO: Verbs.md Modifiers.md Formats.md Reference.md
-->

---

## Overview

ODIN Transform extends the ODIN notation to define data transformations between ODIN documents and external formats. A transform file is itself a valid ODIN document that describes how to convert data—the notation transforms itself.

Transform definitions are declarative mappings: source paths map to target fields through copy operations, transformations, and structural directives. The same transform engine handles fixed-width records, XML hierarchies, delimited files, and JSON output.

---

## License

**Specification:** Creative Commons Attribution 4.0 International (CC-BY 4.0)

**Reference Implementations:** Apache License 2.0

---

## Design Principles

1. **Declarative** — Mappings describe what, not how
2. **Unidirectional** — One transform file, one direction
3. **Self-describing** — Operations are explicit in the syntax
4. **Composable** — Import and reuse common mappings
5. **Format-agnostic** — Same concepts across output formats
6. **Traceable** — Source and target schemas documented
7. **Deterministic** — Same input produces same output

---

## ODIN as Canonical Data Model

ODIN serves as the **Canonical Data Model (CDM)** for all transformations. Regardless of source and target formats, data flows through ODIN as the intermediate representation:

```
┌─────────┐      ┌──────┐      ┌─────────┐
│ JSON    │─────►│      │─────►│ XML     │
├─────────┤      │ ODIN │      ├─────────┤
│ XML     │─────►│ CDM  │─────►│ JSON    │
├─────────┤      │      │      ├─────────┤
│ CSV     │─────►│      │─────►│ CSV     │
├─────────┤      │      │      ├─────────┤
│ Fixed   │─────►│      │─────►│ Fixed   │
└─────────┘      └──────┘      └─────────┘
```

**Why ODIN as CDM?**

1. **Type Preservation:** ODIN's self-describing types (`#`, `##`, `#$`, `?`, `@`, `^`, `~`, `#%`) preserve semantic meaning
2. **Modifier Flow:** Field modifiers (`!` critical, `*` confidential, `-` deprecated) travel with data
3. **Canonical Form:** Enables hashing, signatures, and deduplication
4. **Schema Validation:** ODIN Schema validates the canonical form

**Note:** A transform declared as `json->json` actually executes as `json->odin->json`.

---

## Document Structure

### File Naming Convention

Transform documents should use the `.transform.odin` extension:

```
acord-to-odin.transform.odin
odin-to-json.transform.odin
policy-export.transform.odin
```

### Transform Document Headers

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
id = "org.example.transform.json.policy"
name = "Policy to JSON"
direction = "odin->json"
```

### Confidential Field Enforcement

```odin
{$}
enforceConfidential = "redact"   ; or "mask"

{Customer}
SSN = "@.ssn :confidential"      ; confidential values nullified on output
```

| Value | Behavior |
|-------|----------|
| (not set) | No enforcement |
| `"redact"` | Confidential values become `~` (null) |
| `"mask"` | Strings become asterisks, numbers become `~` |

### Source Schema Declaration

```odin
{$source}
schema = "org.odin.auto.policy/1.0.0"
format = "json"
discriminator = @.recordType      ; For multi-record-type inputs
```

### Target Format Declaration

```odin
{$target}
format = "json"
encoding = "utf-8"
indent = ##2
```

### Constants

```odin
{$const}
DEFAULT_STATE = "TX"
POLICY_VERSION = "2024.1"
MAX_VEHICLES = ##10

; Reference with @$const.NAME
state = @policy.state :default @$const.DEFAULT_STATE
```

### Accumulators

```odin
{$accumulator}
record_count = ##0
premium_total = #$0.00

; Update with %accumulate
_ = %accumulate record_count ##1
_ = %accumulate premium_total @coverage.premium

; Read with @$accumulator.name
total_records = @$accumulator.record_count
```

**Accumulator Scope:**

| Mode | Behavior |
|------|----------|
| Single document | Reset at start of each pass (default) |
| Batch | Reset per-document (default) |
| `._persist = ?true` | Retain across passes |

**Inter-Pass Persistence:**

By default, accumulators reset between passes. To carry values from one pass to the next, set `._persist = ?true`:

```odin
{$accumulator}
unitPrice = ##0
unitPrice._persist = ?true         ; survives pass boundary

subtotal = ##0
subtotal._persist = ?true          ; survives pass boundary
```

Without `._persist`, an accumulator set in pass 1 reads as its initial value (e.g., `##0`) in pass 2.

### The `_` Sink Field

The special target field `_` executes its expression but emits no output. This is how you perform side-effects (like accumulation) without polluting the output:

```odin
{_calcStep}
_ = %accumulate total %multiply @.quantity @.unitPrice   ; accumulates, emits nothing
```

**Constraint:** Only one `_ =` assignment per section. Multiple `_ =` lines in the same section cause a duplicate path error (P007). Use separate `{_stepN}` sections for multiple operations:

```odin
; WRONG — duplicate path error
{_calc}
_ = %accumulate subtotal %multiply @.quantity @.unitPrice
_ = %accumulate tax %multiply @$accumulator.subtotal @$const.taxRate

; CORRECT — separate sections
{_step1}
_ = %accumulate subtotal %multiply @.quantity @.unitPrice

{_step2}
_ = %accumulate tax %multiply @$accumulator.subtotal @$const.taxRate
```

### Computation-Only Sections

Sections that contain only `_ =` sinks produce no output fields and are omitted from the result. Prefix these sections with `_` by convention to signal they are computation-only:

```odin
{_lookupPrice}              ; computation-only — not in output
_ = %accumulate unitPrice %lookup "PRODUCTS.unitPrice" @.sku

{_calcSubtotal}             ; computation-only — not in output
_ = %accumulate subtotal %multiply @$accumulator.unitPrice @.quantity

{order}                     ; output section — appears in result
product = @.name
total = @$accumulator.subtotal
```

Sections execute sequentially within a pass, so accumulators set in `{_lookupPrice}` are immediately readable in `{_calcSubtotal}`.

### Multi-Pass Execution

```odin
{_countVehicles[]}
_pass = ##1
_loop = "@"
_from = "vehicles"
_ = "%accumulate vehicleCount ##1"

{_calcAvg}
_pass = ##2
_ = "%set avgPremium %divide @$accumulator.totalPremium @$accumulator.vehicleCount"

{Vehicles[]}
_pass = ##3
_loop = "@"
_from = "vehicles"
NormalizedPremium = "%divide @.basePremium @$accumulator.avgPremium"

{Summary}
; No _pass — runs after all numbered passes
VehicleCount = "@$accumulator.vehicleCount"
```

### Lookup Tables

```odin
{$table.STATUS[name, code]}
"active", "A"
"pending", "P"
"cancelled", "C"

; Lookup by name, return code — quote "TABLE.column" in verb expressions
status_code = %lookup "STATUS.code" @.statusName

; Reverse lookup (same syntax)
status_name = %lookup "STATUS.name" @.statusCode
```

**Important:** The `TABLE.column` argument must be **quoted** in verb expressions. Unquoted `STATUS.code` is parsed as three tokens (`STATUS`, `.`, `code`) and produces a parse error. Always write `"STATUS.code"`.

**Multi-Column Tables:**

```odin
{$table.TERRITORY[state, zip_prefix, territory, region]}
"TX", "787", "AUSTIN", "CENTRAL"
"TX", "750", "DALLAS", "NORTH"

; Match on multiple columns — "TABLE.column" must be quoted
territory = %lookup "TERRITORY.territory" @.state @.zipPrefix
```

---

## Symbol Reference

| Symbol | Name | Meaning |
|--------|------|---------|
| `@` | Copy | Direct copy from source path |
| `@.` | Current | Current loop item |
| `@$accumulator` | Accumulator | Reference accumulator value |
| `@$const` | Constant | Reference constant value |
| `%` | Transform | Apply built-in verb |
| `%&` | Custom | Apply custom namespaced verb |
| `"..."` | Literal | Constant string value |
| `##N` | Integer | Constant integer value |
| `#N` | Number | Constant numeric value |
| `#$N` | Currency | Constant currency value |
| `:` | Modifier | Modifier follows |

---

## Field Reference Resolution

Understanding how `@` references resolve is critical for writing correct transforms.

### Resolution Rules

| Reference | Resolves From | Example |
|-----------|---------------|---------|
| `@fieldName` | Source data root | `@premium` reads `source.premium` |
| `@.fieldName` | Current context (loop item or source root) | `@.name` in a loop reads item.name |
| `@path.nested` | Source data, nested path | `@policy.number` reads `source.policy.number` |
| `@$const.NAME` | Constants section | `@$const.TAX_RATE` |
| `@$accumulator.NAME` | Accumulator values | `@$accumulator.total` |

### Source-Only Resolution

**`@fieldName` always resolves from source data, never from computed output fields.** This is the most common source of errors in transforms.

```odin
; WRONG — @subtotal refers to source data, not the computed field above
{invoice}
subtotal = %multiply @.quantity @.unitPrice
tax = %multiply @subtotal @$const.taxRate        ; ← @subtotal resolves to source.subtotal (undefined → 0)
total = %add @subtotal @tax                      ; ← both resolve from source, not computed values

; CORRECT — compute inline from source values
{invoice}
subtotal = %multiply @.quantity @.unitPrice
tax = %round %multiply %multiply @.quantity @.unitPrice @$const.taxRate ##2
total = %round %multiply @.quantity %multiply @.unitPrice %add ##1 @$const.taxRate ##2
```

For chained calculations that cannot be expressed inline, use accumulators (see [Computation Patterns](#computation-patterns)).

---

## Mapping Syntax

### Direct Copy

```odin
policy_number = @policy.number
insured_name = @insured.name.last
```

### Constant Values

```odin
record_type = "01"
count = ##0
amount = #$100.00
```

### Transformations

```odin
effective = %formatDate @policy.effective "YYYYMMDD"
name = %concat @name.last ", " @name.first
code = %lookup "STATUS.code" @policy.status
```

### Modifiers

```odin
policy_number = @policy.number :pos 0 :len 15 :rightPad " "
state = @policy.state :default "TX"
amount = @premium :required
```

---

## Computation Patterns

Since `@fieldName` resolves from source data only, chaining computed values requires one of these patterns:

### Pattern 1: Inline Computation

When the calculation can be expressed in terms of source fields, compose verbs inline:

```odin
{$const}
taxRate = #0.0825

{invoice}
subtotal = %multiply @.quantity @.unitPrice
; Tax = quantity × unitPrice × taxRate (all from source/constants)
tax = %round %multiply %multiply @.quantity @.unitPrice @$const.taxRate ##2
; Total = quantity × unitPrice × (1 + taxRate)
total = %round %multiply @.quantity %multiply @.unitPrice %add ##1 @$const.taxRate ##2
```

### Pattern 2: Accumulator Pipeline

When intermediate values are needed across multiple steps, use accumulator sections:

```odin
{$accumulator}
bodyFactor = ##0
adjusted = ##0
premium = ##0

{_step1}
_ = %accumulate bodyFactor %lookup "BODY_TYPES.factor" @.vehicleType

{_step2}
_ = %accumulate adjusted %round %multiply @.basePremium @$accumulator.bodyFactor ##2

{_step3}
_ = %accumulate premium %subtract @$accumulator.adjusted %round %multiply @$accumulator.adjusted @.discountRate ##2

{policy}
adjusted = @$accumulator.adjusted
premium = @$accumulator.premium
```

### Pattern 3: Multi-Pass with Persistence

When computations span passes, use `._persist = ?true`:

```odin
{$accumulator}
unitPrice = ##0
unitPrice._persist = ?true

{_lookupPrice}
_pass = ##1
_ = %accumulate unitPrice %lookup "PRODUCTS.unitPrice" @.sku

{order}
_pass = ##2
price = @$accumulator.unitPrice    ; reads value from pass 1
```

### Verb Nesting Constraints

Verbs with **fixed arity** (e.g., `%multiply` takes 2 args) can nest other verbs as arguments. However, **variadic verbs** (e.g., `%lookup` takes variable args) cannot be nested inside fixed-arity verbs because the parser cannot determine where the variadic arguments end:

```odin
; WRONG — %lookup is variadic, parser can't delimit its args inside %multiply
adjusted = %multiply %lookup "RATES.factor" @.type @.basePremium

; CORRECT — break into accumulator step
{_step1}
_ = %accumulate factor %lookup "RATES.factor" @.type

{_step2}
adjusted = %multiply @$accumulator.factor @.basePremium
```

---

## EBNF Grammar (Core Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Transform Document *)
transform_doc   = { line } ;
line            = ( comment | directive | header | mapping | blank ) , EOL ;

(* Directives *)
directive       = import_directive ;
import_directive = "@import" , whitespace , import_path , [ " as " , identifier ] ;

(* Headers *)
header          = "{" , header_path , "}" ;
header_path     = "$" , [ "." , identifier ]
                | "$table." , identifier , "[" , column_list , "]"
                | "$accumulator"
                | "segment." , identifier , [ "[]" ]
                | xml_path ;

(* Mappings *)
mapping         = target_field , "=" , value_expr , { modifier } ;
target_field    = identifier | "_" ;
value_expr      = copy_expr | transform_expr | literal_expr ;

(* Copy and Transform *)
copy_expr       = "@" , source_path ;
transform_expr  = "%" , verb , { whitespace , argument }
                | "%&" , namespaced_verb , { whitespace , argument } ;

(* Source Paths *)
source_path     = path_segment , { "." , path_segment }
                | "." , { "." , path_segment }
                | "$accumulator." , identifier
                | "$const." , identifier ;
```

---

*See also: [Verbs](Verbs.md) | [Modifiers](Modifiers.md) | [Formats](Formats.md) | [Reference](Reference.md)*
