# ODIN Schema 1.0: Foundations

<!-- LLM:
PURPOSE: Schema notation design principles and symbol reference
KEY SYNTAX: !=required ~=nullable *=confidential -=deprecated ?=bool #=num ##=int #$=currency @=ref ^=binary
SYMBOLS: :(bounds) :/pattern/ (enum) []=array |=union &=intersection :if=conditional :invariant=crossfield
SEE ALSO: Types.md Composition.md Advanced.md Reference.md
-->

---

## Overview

ODIN Schema extends the ODIN notation to describe document structure, types, and constraints. Rather than inventing a separate schema language, ODIN Schema uses ODIN syntax to validate ODIN documents—the notation describes itself.

ODIN Schema inherits ODIN's core principles: self-describing, line-based, flat, diffable, and deterministic. Schema definitions are themselves valid ODIN documents.

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

1. **Self-referential** - ODIN syntax describes ODIN structure
2. **Concise** - Symbols over keywords
3. **Type-aware** - Numeric, boolean, temporal types are distinct
4. **Constraint-driven** - Bounds, patterns, enums in-line
5. **Composable** - Reusable type definitions
6. **Familiar** - Borrows from programming language conventions
7. **Traceable** - Schema derivation documented for provenance

---

## Design Philosophy

### Why Schema in ODIN Syntax?

Most schema languages are separate languages from their data formats. JSON Schema is JSON describing JSON. XML Schema (XSD) is XML describing XML. But they're verbose, complex, and require learning two syntaxes.

ODIN Schema uses ODIN to describe ODIN. A schema file is itself a valid ODIN document. The same parser, the same syntax, the same mental model. If you know ODIN, you know 90% of ODIN Schema—the rest is just constraint notation.

This isn't just aesthetic. It means:
- **One parser, two purposes:** Your ODIN parser can read schemas with minimal extension
- **Schemas are diffable:** Same line-based, flat structure means meaningful git diffs
- **Schemas compose:** Import, chain, and overlay schemas just like data documents
- **Self-validation:** The schema schema can validate itself

### Why Symbols for Constraints?

Schema definitions are read repeatedly—every time you validate data, every time you review a PR, every time you onboard a new developer. Verbosity compounds. Compare:

**JSON Schema:**
```json
{
  "type": "object",
  "properties": {
    "vin": { "type": "string", "minLength": 17, "maxLength": 17, "pattern": "^[A-HJ-NPR-Z0-9]{17}$" },
    "year": { "type": "integer", "minimum": 1900, "maximum": 2100 },
    "price": { "type": "number", "minimum": 0 }
  },
  "required": ["vin", "year"]
}
```

**ODIN Schema:**
```odin
{vehicle}
vin = !:(17):/^[A-HJ-NPR-Z0-9]{17}$/
year = !##:(1900..2100)
price = #$:(0..)
```

Same constraints, fraction of the tokens. The symbols become second nature: `!` means required, `##` means integer, `:(min..max)` means bounds, `:/regex/` means pattern.

### Why Inline Constraints?

ODIN Schema puts constraints next to the field they constrain:

```odin
email = !:/^[^@]+@[^@]+\.[^@]+$/
age = ##:(0..150)
status = (pending, active, closed)
```

The field declaration tells you everything: name, type, requirement, bounds, pattern, enum values. When you ask "what are the rules for `age`?", you want one place to look.

### Why Discriminated Unions?

Real-world data is polymorphic. A payment might be credit card, bank transfer, or PayPal—each with different required fields.

ODIN Schema's conditional fields (`:if`) handle this cleanly:

```odin
{payment}
method = !(card, bank, paypal)
card_number = !*:(16):if method = card
routing_number = !:(9):if method = bank
paypal_email = !:if method = paypal
```

The schema says exactly what it means: `card_number` is required if and only if `method = card`.

### Why Schema Derivation?

Schemas in regulated industries don't come from nowhere. They implement requirements: government regulations, industry mandates, contractual obligations. The `$derivation` header documents provenance:

```odin
{$derivation}
source[0].authority = "Texas Department of Insurance"
source[0].citation = "TX Admin Code Title 28, Part 1, Chapter 5"
methodology = "regulatory_derivation"
```

---

## Symbol Reference

| Symbol | Meaning | Context |
|--------|---------|---------|
| `!` | Required | Field must be present |
| `?` | Boolean | Boolean type |
| `#` | Number | Numeric type (any precision) |
| `##` | Integer | Whole number only |
| `#.N` | Decimal | Fixed N decimal places |
| `#$` | Currency | Money (defaults to 2 decimals) |
| `#$.N` | Currency | Money with N decimal places |
| `#%` | Percent | Percentage as decimal (0-1) |
| `@` | Reference | Path reference to another field |
| `^` | Binary | Base64-encoded binary data |
| `~` | Nullable/Null | Nullable modifier (prefix) or null type (in union) |
| `*` | Confidential | Value contains sensitive data; signals downstream systems to handle accordingly |
| `-` | Deprecated | Field is obsolete |
| `:` | Constraint | Constraint expression follows |
| `()` | Enum/Bounds | Enumeration or numeric range |
| `[]` | Array | Array type definition |
| `:X...X` | Pattern | Regular expression (X = delimiter) |
| `\|` | Union | Type separator |
| `&` | Intersection | Combine type definitions |
| `:if` | Conditional | Field depends on another field's value |
| `:unless` | Inverse Conditional | Field required when condition is FALSE |
| `:of` | Cardinality | N-of-M field constraint |
| `:invariant` | Invariant | Cross-field validation expression |
| `:format` | Format Validation | String format (email, url, uuid, etc.) |
| `:computed` | Computed Field | Derived value, not in input |
| `:immutable` | Immutable Field | Cannot be changed after creation |

---

## EBNF Grammar (Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Schema Document *)
schema_doc      = { line } ;
line            = ( comment | header | definition | object_constraint | blank ) , EOL ;

(* Headers *)
header          = "{" , header_type , "}" ;
header_type     = metadata_header | derivation_header | type_def_header | array_header | path_header ;
metadata_header = "$" ;
derivation_header = "$derivation" ;
type_def_header = "@" , [ namespace ] , identifier ;
namespace       = "&" , reverse_dns , "." ;

(* Modifiers *)
modifiers       = { modifier } ;
modifier        = "!" | "~" | "*" | "-" ;

(* Primitives *)
identifier      = letter , { letter | digit | "_" } ;
path            = identifier , { "." , identifier } ;
```

---

*See also: [Types](Types.md) | [Composition](Composition.md) | [Advanced](Advanced.md) | [Reference](Reference.md)*
