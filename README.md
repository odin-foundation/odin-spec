# ODIN Specification

**Open Data Interchange Notation — Version 1.0**

ODIN is a data interchange format designed for the AI era — combining the token efficiency of CSV, the nesting capability of JSON, the type safety of Protocol Buffers, and the human readability of TOML, all in a single notation.

```odin
{$}
odin = "1.0.0"
id = "example"

{order}
id = "ORD-12345"
placed = 2025-01-15T09:30:00Z
total = #$299.97

{order.items[] : sku, name, qty, price}
"W-100", "Widget", ##10, #$29.99
"G-200", "Gadget", ##5, #$149.50
```

## Specifications

| Document | Description | Status |
|----------|-------------|--------|
| [ODIN 1.0](ODIN_1_0_Specification.md) | Core notation — syntax, types, paths, arrays, canonical form, document chaining | 1.0 |
| [Schema Language](schema/) | Schema definition language — types, constraints, composition, validation | 1.0 |
| [Transform Engine](transform/) | Declarative data transforms — mapping syntax, verbs, format configuration | 1.0 |
| [Forms 1.0](ODIN_Forms_1_0_Schema.md) | Declarative form layout for print and screen rendering | 1.0 |

### Schema Language

The schema spec is split into focused documents:

| Document | Content |
|----------|---------|
| [Foundations](schema/Foundations.md) | Design principles, symbol reference, derivation |
| [Types](schema/Types.md) | Type definitions, constraints, defaults |
| [Composition](schema/Composition.md) | Modifiers, arrays, reusable types, imports |
| [Advanced](schema/Advanced.md) | Conditionals, invariants, format patterns |
| [Reference](schema/Reference.md) | Full EBNF grammar, validation rules |

### Transform Engine

The transform spec is split into focused documents:

| Document | Content |
|----------|---------|
| [Core](transform/Core.md) | Document structure, mapping syntax, lookup tables |
| [Verbs](transform/Verbs.md) | All transformation verbs and their arities |
| [Modifiers](transform/Modifiers.md) | Field modifiers, segments, confidential enforcement |
| [Formats](transform/Formats.md) | Format-specific configuration (JSON, XML, CSV, fixed-width) |
| [Reference](transform/Reference.md) | Complete examples, EBNF grammar, error codes |

## Key Concepts

### Canonical Data Model

ODIN serves as the **Canonical Data Model (CDM)** for all data transformations. All format conversions route through ODIN as the intermediary — there is no direct JSON-to-XML or CSV-to-JSON path. Every transform is either an ingest (source → ODIN) or an emit (ODIN → target).

```
+----------+      +------+      +----------+
| JSON     |----->|      |----->| XML      |
+----------+      |      |      +----------+
| XML      |----->| ODIN |----->| JSON     |
+----------+      | CDM  |      +----------+
| CSV      |----->|      |----->| CSV      |
+----------+      |      |      +----------+
| Fixed    |----->|      |----->| Fixed    |
+----------+      +------+      +----------+
```

This architecture means:
- **Type preservation** — ODIN's self-describing types (`#`, `##`, `#$`, `?`, `@`, `^`, `~`) preserve semantic meaning through every conversion
- **Modifier flow** — Field modifiers (`!` required, `*` confidential, `-` deprecated) travel with data across system boundaries
- **Canonical form** — Deterministic output enables hashing, digital signatures, and deduplication at the intermediary layer
- **Single validation point** — Schema validation occurs on the ODIN representation, not on every source/target format

### Self-Describing Types

Every value declares its own type via prefix — no schema lookup required to parse:

| Prefix | Type | Example |
|--------|------|---------|
| `"..."` | String | `"Honda"` |
| `#` | Number | `#3.14` |
| `##` | Integer | `##42` |
| `#$` | Currency | `#$99.99:USD` |
| `#%` | Percent | `#%0.15` |
| `?` | Boolean | `?true` |
| `~` | Null | `~` |
| `@` | Reference | `@policy.insured` |
| `^` | Binary | `^sha256:e3b0c44...` |
| `%` | Verb | `%upper @name` |

Temporal types are detected by pattern: `2024-06-15` (date), `2024-06-15T14:30:00Z` (timestamp), `T14:30:00` (time), `P1Y6M` (duration).

### Inline Modifiers

Metadata travels with the value, not in a separate schema:

| Modifier | Symbol | Meaning |
|----------|--------|---------|
| Critical | `!` | Field is required — absence fails validation |
| Confidential | `*` | Value contains sensitive data — signals downstream systems |
| Deprecated | `-` | Field is obsolete — consumers should migrate |

```odin
name = !"John Smith"           ; required
ssn = *"123-45-6789"           ; confidential — real value stored, modifier signals sensitivity
email = !*"john@example.com"   ; required AND confidential
```

### Canonical Form

Two ODIN documents with the same logical content produce identical byte sequences in canonical form — enabling content addressing, deduplication, digital signatures, and caching.

## Implementations

| Language | Package | Repository |
|----------|---------|------------|
| TypeScript | `@odin-foundation/core` | [odin-core-typescript](https://github.com/odin-foundation/odin-core-typescript) |
| .NET | | [odin-core-dotnet](https://github.com/odin-foundation/odin-core-dotnet) |
| Java | `foundation.odin:odin-core` | [odin-core-java](https://github.com/odin-foundation/odin-core-java) |
| Python | | [odin-core-python](https://github.com/odin-foundation/odin-core-python) |
| Ruby | | [odin-core-ruby](https://github.com/odin-foundation/odin-core-ruby) |
| Rust | | [odin-core-rust](https://github.com/odin-foundation/odin-core-rust) |

Cross-language conformance is verified by the [ODIN Golden Test Suite](https://github.com/odin-foundation/odin-golden-tests).

## Schema Library

337 production-ready schemas across 18 industries — insurance, healthcare, finance, legal, government, and more. See the [ODIN Schema Library](https://github.com/odin-foundation/odin-schemas).

## License

**Specification:** Creative Commons Attribution 4.0 International (CC-BY 4.0)

**Reference Implementations:** Apache License 2.0

## Learn More

- [odin.foundation](https://odin.foundation) — Documentation, schema browser, and tools
- [ODIN Schema Library](https://odin.foundation/schemas) — Browse all 337 schemas online
- [Transform Lab](https://odin.foundation/transform-lab) — Interactive transform playground
