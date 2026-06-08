# ODIN Changelog

Specification and SDK changes. The six SDKs share these semantics and are released
together; see each spec's Version History for per-document detail.

## 1.2.0 — 2026-06

New transform verbs, the `%expr` formula macro, and three engine-behavior
corrections, shipped across all six SDKs (TypeScript, Rust, .NET, Java, Python,
Ruby) and pinned by the shared golden corpus.

### Transform verbs
- Object: `pick`, `omit`, `fromEntries`, `invert`, `defaults`, `renameKeys`, `compactObject`.
- Array set operations and collection: `union`, `intersection`, `difference`, `symmetricDifference`, `keyBy`, `countBy`, `window`, `explode`.
- Predicate aggregation: `countIf`, `sumIf`, `avgIf`.
- Math: `gcd`, `lcm`, `factorial`.
- Encoding / web: `buildUrl`, `parseUrl`, `buildQuery`, `parseQuery`, `hmac`.
- Canonical: `canonicalHash`, `stableStringify`.
- String / markup: `escapeHtml`, `unescapeHtml`, `escapeXml`, `stripTags`, `template`.
- Dated cash flow: `xnpv`, `xirr`.

### `%expr` formula macro
- Parse-time macro compiling an infix arithmetic formula to a verb tree: `+ - * / %`, unary `-`, and `^` (right-associative); functions `abs`, `floor`, `ceil`, `trunc`, `round`, `sqrt`, `pow`, `min`, `max`.
- Precedence: calls/parens > `^` > unary > `* / %` > `+ -`, so `-2^2` is `-4`.
- Variables resolve under an explicit bindings reference (`%expr "a+b" @.vars`); a variable with no bindings is compile error `T015`.

### Engine behavior
- Multiple accumulators advance in a single loop pass; every `_`-prefixed target is a computation-only sink that runs but is not emitted.
- Control-flow verbs (`ifElse`, `ifNull`, `ifEmpty`, `coalesce`, `and`, `or`, `cond`, `switch`) evaluate the condition and only the selected branch, so `and`/`or`/`coalesce` short-circuit and unselected branch side effects do not fire (strict mode still evaluates eagerly).
- `addMonths` / `addYears` clamp to the last day of the target month.

### Fixes
- `convertUnit` pound mass constant corrected to `0.45359237` kg.

### Schema library
- The canonical schema library (`@odin-foundation/schemas`) is now **permissive by default**: every `!` required marker was removed across all 302 schema files. Fields declare type and constraints but are left optional, because presence is a property of a consumer's context (jurisdiction, workflow state, filing), not of the data model. Consumers add requirements by importing a type and tightening it with `:override` (optional to required). No SDK or validator behavior changed. This is a schema-data and documentation change only.

## 1.1.0 — 2026-05

A conformance pass aligning all six SDKs (TypeScript, Rust, .NET, Java, Python, Ruby)
with the specifications, plus the features below. Every change is enforced by the
shared golden suite.

### Transform
- Conditional segments (`:if` / `_if`) and `:elif` / `:else` chains; a dangling branch is error `T012`.
- Conditions are verb expressions (`%and` / `%or` / `%not` + comparison verbs); legacy quoted-infix is retained.
- Field modifiers `:validate` / `:enum` / `:range` (error `T013`), `:object` / `:raw` / `:array`, and comparison `:if` / `:unless`.
- Nested loops: a segment may declare multiple `:loop path :as alias` lines, iterated as a cross-product with per-alias resolution and innermost-`:counter` reset.
- Triple-quoted (`"""`) multiline strings, and `:literal` segment blocks rendered per loop iteration with `${...}` interpolation of paths, current-item fields, verbs, and accumulators; `\${` / `\$` / `\\` escapes; a nested interpolation is error `T014`.
- Bare and header-inline `:loop` / `:counter` / `:from`; XML `:cdata`; fixed-width line padding; `##@` / `#$@` reference coercion.
- Verb fixes (`toObject`, `nextBusinessDay`, `formatDuration`); the full verb surface is now documented.

### Core format
- Canonical form round-trips (top-level `$.path` assignments parse and re-serialize).
- `##` rejects fractional values; `@$.path` meta references parse.
- `parseDocuments` exposes `---` document chains.
- Typed tabular cells (integer, currency, negative, etc.) parse in every column position without dropping or shifting a column.

### Schema
- Type intersection (`@a & @b`), chronological temporal range bounds, percent type, typed defaults,
  full union members and null members, a trailing `:if` after a pattern, `:computed` / `:immutable`
  on temporal types, recursive `@type` field enforcement, and the invariant null-operand rule.

### Forms
- Page templates, regions with bound repetition and overflow, render-time interpolation, inline field
  values, `select` options, barcode `type`, `img` background, `inputType`, and per-side margins.
  Implemented across TypeScript, .NET, and Java.

## 1.0.0 — 2024-12

Initial specifications: core format, schema language, transform engine (including multi-pass
execution and the financial/statistical verbs), and forms.
