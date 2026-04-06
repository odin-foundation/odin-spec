# ODIN Transform 1.0: Reference

<!-- LLM:
PURPOSE: Complete examples, EBNF grammar, error handling, and version history
EXAMPLES: odin->json, json->odin, odin->xml, xml->odin, odin->fixed-width, fixed-width->odin
ERROR TYPES: SOURCE_MISSING TYPE_MISMATCH VALIDATION_FAILED LOOKUP_FAILED FORMAT_ERROR
PROCESSING: Parse→Validate→Initialize→Process→Finalize
VERSION: 1.2 (multi-pass), 1.1 (financial verbs), 1.0 (initial)
SEE ALSO: Core.md Verbs.md Modifiers.md Formats.md
-->

---

## Complete Examples

### Example 1: ODIN ↔ JSON

**ODIN → JSON:**

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "odin->json"

{$target}
format = "json"
indent = ##2

{policy}
id = @policy.id
number = @policy.number
status = @status
effectiveDate = @effective

{policy.insured}
name = %concat @insured.name.first " " @insured.name.last
address = :object {street = @insured.address.line1, city = @insured.address.city, state = @insured.address.state, zip = @insured.address.zip}

{policy.vehicles[]}
:loop vehicles
vin = @.vin
year = @.year :type number
make = @.make
premium = @.premium :type number
garaging = :object {city = @.garaging.city, state = @.garaging.state} :omitNull
```

**JSON → ODIN:**

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "json->odin"

{$source}
format = "json"

{policy}
id = @policy.id
number = @policy.number
status = @policy.status
effective = %coerceDate @policy.effectiveDate

{vehicles[]}
:loop policy.vehicles
vin = @.vin
year = ##@.year
make = @.make
premium = #$@.premium
```

### Example 2: ODIN ↔ XML

**ODIN → XML:**

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "odin->xml"

{$target}
format = "xml"
indent = ##2

{Policy}
id = @policy.id :attr
Number = @policy.number
Status = @status
EffectiveDt = %formatDate @effective "YYYY-MM-DD"

{Policy.Vehicles.Vehicle[]}
:loop vehicles
seq = @_index :attr
VIN = @.vin
Year = @.year
Make = @.make :upper
```

**XML → ODIN:**

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "xml->odin"

{$source}
format = "xml"

{policy}
id = @Policy.id :attr
number = @Policy.Number
status = @Policy.Status
effective = %parseDate @Policy.EffectiveDt "YYYY-MM-DD"

{vehicles[]}
:loop Policy.Vehicles.Vehicle
vin = @.VIN
year = %coerceInteger @.Year
make = @.Make :lower
```

### Example 3: ODIN ↔ Fixed-Width

**ODIN → Fixed-Width:**

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "odin->fixed-width"

{$target}
format = "fixed-width"
lineWidth = ##80

{$table.STATUS[name, code]}
"active", "A"
"pending", "P"

{segment.HDR}
rec = "01" :pos 0 :len 2
pol = @policy.number :pos 2 :len 15 :rightPad " "
eff = %formatDate @effective "MMDDYYYY" :pos 17 :len 8
st = %lookup "STATUS.code" @.status :pos 25 :len 1

{segment.VEH[]}
:loop vehicles
rec = "20" :pos 0 :len 2
vin = @.vin :pos 2 :len 17
yr = @.year :pos 19 :len 4
```

**Fixed-Width → ODIN:**

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "fixed-width->odin"

{$source}
format = "fixed-width"
discriminator = ":pos 0 :len 2"

{$table.STATUS[code, name]}
"A", "active"
"P", "pending"

{segment.HDR :type "01"}
policy.number = @. :pos 2 :len 15 :trim
policy.effective = %parseDate @. :pos 17 :len 8 "MMDDYYYY"
policy.status = %lookup "STATUS.name" @. :pos 25 :len 1

{segment.VEH[] :type "20"}
vehicles[].vin = @. :pos 2 :len 17 :trim
vehicles[].year = %coerceInteger @. :pos 19 :len 4
```

---

## Formal Grammar (EBNF)

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
column_list     = column_name , { "," , whitespace , column_name } ;
column_name     = identifier ;

(* Mappings *)
mapping         = target_field , "=" , value_expr , { modifier } ;
target_field    = identifier | "_" ;
value_expr      = copy_expr | transform_expr | literal_expr ;

(* Copy and Transform *)
copy_expr       = "@" , source_path ;
transform_expr  = "%" , verb , { whitespace , argument }
                | "%&" , namespaced_verb , { whitespace , argument } ;
verb            = identifier ;
namespaced_verb = identifier , { "." , identifier } ;

(* Lookup Expression *)
lookup_expr     = "%lookup" , whitespace , table_ref , { whitespace , match_value } ;
table_ref       = identifier , "." , column_name ;
match_value     = copy_expr | literal_expr ;

(* Literals *)
literal_expr    = quoted_string | number_literal | boolean_literal | "~" ;
number_literal  = "#$" , digits , "." , digit , digit    (* currency *)
                | "##" , [ "-" ] , digits                 (* integer *)
                | "#" , [ "-" ] , digits , [ "." , digits ] ;

(* Source Paths *)
source_path     = path_segment , { "." , path_segment }
                | "." , { "." , path_segment }
                | "$accumulator." , identifier
                | "$const." , identifier ;
path_segment    = identifier , [ "[" , integer , "]" ] ;

(* Modifiers *)
modifier        = ":" , identifier , [ whitespace , modifier_value ] ;

(* Interpolation in Literal Blocks *)
literal_block   = '"""' , { literal_char | interpolation } , '"""' ;
interpolation   = "${" , ( copy_expr | transform_expr ) , "}" ;
literal_char    = "\\" , ( "$" | "\\" ) | any_char ;

(* Primitives *)
identifier      = ( letter | "_" ) , { letter | digit | "_" } ;
quoted_string   = '"' , { "\\" , any | unescaped } , '"' ;
```

---

## Error Handling

### Error Types

| Error | Description | Behavior |
|-------|-------------|----------|
| `SOURCE_MISSING` | Required path not found | Per `onMissing` |
| `TYPE_MISMATCH` | Incompatible type | Fail |
| `VALIDATION_FAILED` | Constraint violation | Per `onValidation` |
| `LOOKUP_FAILED` | Not in table | Use `:default` or fail |
| `FORMAT_ERROR` | Cannot format value | Fail |

### Error Output

```odin
{$error}
code = "SOURCE_MISSING"
message = "Required path 'policy.number' not found"
segment = "segment.HDR"
field = "policy_number"
```

---

## Implementation Notes

### Processing Model

1. **Parse** — Load transform, resolve imports
2. **Validate** — Check transform structure (shallow only)
3. **Initialize** — Reset accumulators
4. **Process** — Evaluate segments in order
5. **Finalize** — Complete output format

### Output Generation

- **Fixed-width** — Build character buffer per line
- **XML** — Build/stream elements with nesting
- **JSON** — Build object tree, serialize at end
- **CSV** — Collect fields, join with delimiter

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.2.0 | 2025-12 | Multi-pass execution |
| 1.1.0 | 2025-12 | Financial & statistical verbs |
| 1.0.0 | 2024-12 | Initial specification |

### v1.2 Features

- Multi-pass execution with `_pass` directive
- Accumulator reset behavior per pass
- All passes emit output (later passes can overwrite)

### v1.1 Features

- **Mathematical:** log, ln, log10, exp, pow, sqrt
- **Time Value of Money:** compound, discount, pmt, fv, pv
- **Statistics:** std, stdSample, variance, varianceSample, median, mode, percentile, quantile
- **Correlation:** covariance, correlation
- **Utility:** clamp, interpolate, weightedAvg

### v1.0 Features

- Transform document structure with constants (`$const`)
- Input discriminators for multi-record-type files
- Copy operations (`@`) and transformation verbs (`%`)
- Custom verb extensibility (`%&namespace.verb`) with reserved namespaces
- **String:** concat, upper, lower, capitalize, titleCase, trim, length, contains, startsWith, endsWith, substring, replace, split, join, mask
- **Numeric:** formatNumber, round, abs, add, subtract, multiply, divide
- **Date/Time:** formatDate, parseDate, addDays, dateDiff, today, now
- **Lookup:** lookup with column selection, lookupDefault
- **Aggregation:** accumulate, sum, count, min, max, avg, first, last
- **Conditional:** coalesce, ifElse, ifNull, ifEmpty, switch
- **Encoding:** base64Encode/Decode, urlEncode/Decode, jsonEncode/Decode
- **Array:** at, filter, flatten, distinct, sort, reverse
- **Generation:** uuid, sequence
- **Type coercion:** coerceString, coerceNumber, coerceInteger, coerceBoolean, coerceDate
- Modifier system (positioning, formatting, conditional, validation)
- Segment headers with loops, counters, and type dispatch
- Accumulators with configurable reset/persist behavior
- Lookup tables with named columns
- Format-specific output (fixed-width, XML, JSON, CSV)
- Format-specific input with namespace and predicate support
- Custom output format extensions (`&namespace.format`)
- Literal blocks with interpolation (no nesting)
- Error handling configuration
- Import and composition

---

*See also: [Core](Core.md) | [Verbs](Verbs.md) | [Modifiers](Modifiers.md) | [Formats](Formats.md)*
