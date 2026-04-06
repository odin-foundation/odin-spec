# ODIN Transform 1.0: Formats

<!-- LLM:
PURPOSE: Format-specific configuration, validation behavior, and imports
FIXED-WIDTH: lineWidth, padChar, truncate, lineEnding
XML: declaration, indent, omitEmpty, namespace prefixes
JSON: indent, nulls(omit/include), emptyArrays
CSV: delimiter, quote, header, lineEnding
VALIDATION: Shallow only (type compatibility, :required, :enum, :range) - use Schema validator for deep validation
ERROR CONFIG: onError, onMissing, onValidation (fail|warn|skip)
IMPORTS: @import ./path.odin [as alias]
SEE ALSO: Core.md Verbs.md Modifiers.md Reference.md
-->

---

## Format-Specific Configuration

### Fixed-Width Format

```odin
{$target}
format = "fixed-width"
encoding = "ascii"
lineEnding = "\r\n"
lineWidth = ##200
padChar = " "
truncate = ?true
```

| Setting | Type | Description |
|---------|------|-------------|
| `lineWidth` | Integer | Fixed line width in characters |
| `padChar` | String | Default pad character |
| `truncate` | Boolean | Truncate fields exceeding length |
| `lineEnding` | String | Line ending (`\n`, `\r\n`) |
| `encoding` | String | Character encoding |

### XML Format

```odin
{$target}
format = "xml"
encoding = "utf-8"
declaration = ?true
indent = ##2
omitEmpty = ?true

{$target.namespace}
ins = http://www.example.org/insurance/xml/
```

| Setting | Type | Description |
|---------|------|-------------|
| `declaration` | Boolean | Include XML declaration |
| `indent` | Integer | Indentation spaces (0 = minified) |
| `omitEmpty` | Boolean | Omit empty elements |
| `encoding` | String | Character encoding |

**XML Namespaces:**

```odin
{$target.namespace}
ins = http://www.example.org/insurance/
xsi = http://www.w3.org/2001/XMLSchema-instance

{Policy}
PolicyNumber = @policy.number :ns ins
```

### JSON Format

```odin
{$target}
format = "json"
encoding = "utf-8"
indent = ##2
nulls = "omit"
emptyArrays = "include"
```

| Setting | Type | Description |
|---------|------|-------------|
| `indent` | Integer | Indentation spaces (0 = minified) |
| `nulls` | String | `omit` or `include` null values |
| `emptyArrays` | String | `omit` or `include` empty arrays |
| `encoding` | String | Character encoding |

### CSV/Delimited Format

```odin
{$target}
format = "csv"
delimiter = ","
quote = "\""
header = ?true
lineEnding = "\n"
```

| Setting | Type | Description |
|---------|------|-------------|
| `delimiter` | String | Field delimiter |
| `quote` | String | Quote character |
| `header` | Boolean | Include header row |
| `lineEnding` | String | Line ending |

### Custom Formats

Custom output formats use namespaced identifiers:

```odin
{$target}
format = &com.acme.proprietary
version = "2.0"
customSetting = ?true
```

Implementations must provide format handlers for custom formats.

---

## Validation Behavior

### Shallow Validation Only

**Important:** Transform-time validation performs shallow checks only:
- Type compatibility (string vs number vs date)
- Constraint modifiers (`:required`, `:enum`, `:range`, `:validate`)
- Path resolution (source paths exist)

Transform validation does **not** perform:
- Full schema invariant evaluation
- Cross-field business rules
- Deep semantic validation

For complete validation, pipe output through the ODIN Schema validator separately.

### Modifier Compatibility

Validators should warn on format-incompatible modifiers:

| Modifier | Valid For | Invalid For |
|----------|-----------|-------------|
| `:pos`, `:len` | fixed-width | xml, json, csv |
| `:attr`, `:ns`, `:cdata` | xml | fixed-width, json, csv |
| `:type`, `:raw` | json | fixed-width, xml, csv |
| `:header` | csv | fixed-width, xml, json |

### Error Configuration

Error handling is configured at the target level:

```odin
{$target}
onError = "fail"       ; fail | warn | skip
onMissing = "skip"     ; fail | warn | skip | default
onValidation = "fail"  ; fail | warn | skip
```

| Setting | Values | Description |
|---------|--------|-------------|
| `onError` | `fail` | Stop transform on any error (default) |
| | `warn` | Log warning, continue |
| | `skip` | Silently skip failed field/record |
| `onMissing` | `fail` | Error if required source path missing |
| | `warn` | Log warning, use null/empty |
| | `skip` | Silently skip field (default) |
| | `default` | Use field's `:default` value |
| `onValidation` | `fail` | Error if validation fails (default) |
| | `warn` | Log warning, emit value anyway |
| | `skip` | Silently skip failed field |

**Modifier Interactions:**
- `:required` overrides `onMissing` — always fails if missing
- `:default "value"` provides fallback when `onMissing = default`
- `:optional` explicitly marks field as skippable

---

## Imports and Composition

### Import Directive

```odin
@import ./tables/common.odin
@import ./mappings/base.odin as base
```

### Alias Usage

```odin
@import ./codes.odin as codes
body_code = %lookup codes.BODY_TYPES @body_type
```

### Import Use Cases

**Shared Lookup Tables:**

```odin
;; tables/territories.odin
{$table.TERRITORY[state, zip_prefix, territory, region]}
"TX", "750", "DALLAS", "NORTH"
"TX", "787", "AUSTIN", "CENTRAL"
; ... thousands of entries ...

;; transforms/policy-export.odin
@import ./tables/territories.odin

{segment.VEH[]}
:loop vehicles
territory = %lookup "TERRITORY.territory" @.state @.zipPrefix
```

**Shared Mappings:**

```odin
;; mappings/address.odin
{@address}
line1 = @.line1
city = @.city
state = @.state
zip = @.zip

;; transforms/customer-export.odin
@import ./mappings/address.odin as addr

{customer.billing}
= @addr.address :from @.billing_address
```

---

## EBNF Grammar (Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Directives *)
import_directive = "@import" , whitespace , import_path , [ " as " , identifier ] ;
import_path     = "./" , path_string | "http" , "s" , "://" , url ;

(* Target Configuration *)
target_header   = "{" , "$target" , "}" ;
target_setting  = identifier , "=" , setting_value ;
setting_value   = quoted_string | number_literal | boolean_literal ;

(* Format-specific *)
namespace_header = "{" , "$target.namespace" , "}" ;
namespace_decl  = identifier , "=" , uri ;
```

---

*See also: [Core](Core.md) | [Verbs](Verbs.md) | [Modifiers](Modifiers.md) | [Reference](Reference.md)*
