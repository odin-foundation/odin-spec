# Transform Execution Limits

PURPOSE: Environment-configurable bounds on transform execution.
KEY LIMITS: MAX_TRANSFORM_FUEL, TRANSFORM_TIMEOUT_MS, MAX_EXPRESSION_DEPTH
SEE ALSO: Core.md Reference.md

## Limits

The transform engine can bound execution so a runaway or over-computing transform
cannot block the host. The bounds are configured through the standard `ODIN_`
environment overrides, alongside the other engine limits.

| Limit | Environment variable | Default | Effect |
|-------|----------------------|---------|--------|
| `MAX_TRANSFORM_FUEL` | `ODIN_MAX_TRANSFORM_FUEL` | `0` | Bounds evaluation work. `0` means unbounded. Exceeding it raises `T016`. |
| `TRANSFORM_TIMEOUT_MS` | `ODIN_TRANSFORM_TIMEOUT_MS` | `0` | Bounds elapsed wall-clock time, in milliseconds. `0` means unbounded. Exceeding it raises `T017`. |
| `MAX_EXPRESSION_DEPTH` | `ODIN_MAX_EXPRESSION_DEPTH` | `32` | Bounds expression evaluation depth. Exceeding it raises `T018`. |

With `MAX_TRANSFORM_FUEL` and `TRANSFORM_TIMEOUT_MS` left at their `0` default, a
transform runs to completion; these bounds take effect only once a limit is set.

Each bound can also be set per call through the transform options, overriding the
global value for that one execution: an option that is provided wins, and an
option left unset falls back to the global limit. Because `0` means unbounded, a
call may pass `0` to opt a single transform out of a global cap.

Fuel measures evaluation work: roughly one unit for each expression evaluated,
plus units proportional to the length of any array an array verb (sort, grouping,
aggregation, set, and shape operations) processes. The exact unit counts are
implementation-specific and are not part of the contract. Treat the cap as a
generous runaway backstop sized to your expected inputs rather than a precise
budget, and raise it if it rejects legitimate transforms.

An exceeded bound produces a **failed** `TransformResult` (`success = false`) whose
`errors` carry the code and the cap value. A limit abort is not downgraded by the
target `onError` policy (`fail` / `warn` / `skip`) and is never thrown past the
execute boundary, so the "execute always returns a result" contract holds.

## Error codes

| Code | Name | Raised when |
|------|------|-------------|
| `T016` | `TRANSFORM_BUDGET_EXCEEDED` | evaluation work exceeds `MAX_TRANSFORM_FUEL` |
| `T017` | `TRANSFORM_TIMEOUT_EXCEEDED` | elapsed time exceeds `TRANSFORM_TIMEOUT_MS` |
| `T018` | `EXPRESSION_DEPTH_EXCEEDED` | evaluation depth exceeds `MAX_EXPRESSION_DEPTH` |

## Scope

These limits bound work that crosses expression evaluation: array verbs over large
inputs, nested expressions, and input shape. They do not interrupt a single native
verb call that does not re-enter expression evaluation, such as a regular
expression inside a validate verb or a custom verb's internal loop.
