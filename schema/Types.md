# ODIN Schema 1.0: Types

<!-- LLM:
PURPOSE: Type definitions and constraint syntax
TYPES: string(default) ?=bool #=num ##=int #.N=decimal #$=currency #$.N #%=percent date timestamp time duration @=ref ^=binary ~=null
UNION: |=chain types, |""=add string, |~=add null, e.g. #|?|~|""
CONSTRAINTS: :(N)=exact :(min..)=min :(..max)=max :(min..max)=range :/regex/=pattern (a,b,c)=enum
DEFAULTS: field = type constraint default_value (e.g. status = (a,b) "a")
SEE ALSO: Foundations.md Composition.md Advanced.md Reference.md
-->

---

## Type Definitions

### String (Default)

String is the default type when no prefix is specified.

```odin
name = !                           ; required string
description =                      ; optional string
code = !:(3..10)                   ; required, length 3-10
pattern = :/^[A-Z]{3}$/            ; regex pattern
status = (draft, published)        ; enum
```

### Boolean

Use `?` for boolean fields.

```odin
active = ?                         ; optional boolean
required = !?                      ; required boolean
enabled = ? ?true                  ; optional, default true
```

### Numeric Types

| Type | Syntax | Description |
|------|--------|-------------|
| Any number | `#` | Integer or decimal |
| Integer | `##` | Whole numbers only |
| Decimal | `#.N` | Fixed N decimal places |
| Currency | `#$` | Money (2 decimal places) |
| Currency | `#$.N` | Money with N decimals |
| Percent | `#%` | Percentage as decimal (0-1) |

```odin
count = ##                         ; optional integer
quantity = !##                     ; required integer
price = #$                         ; optional currency
total = !#$                        ; required currency
rate = #.4                         ; 4 decimal places
amount = #                         ; any numeric precision
btc_amount = #$.8                  ; bitcoin (8 decimals)
tax_rate = #%                      ; percentage (0.15 = 15%)
```

**Schema vs Data Syntax:**

| Schema Declaration | Data Value |
|--------------------|------------|
| `rate = #.4` | `rate = #0.0500` |
| `btc = #$.8` | `btc = #$1.00000000` |
| `count = ##` | `count = ##42` |
| `price = #$` | `price = #$99.99` |
| `tax = #%` | `tax = #%0.15` |

**Precision Semantics:**

The `#.N` constraint means **exactly N decimal places**:
- `#.4` requires exactly 4 decimal places: `#0.0500` ✓, `#0.05` ✗
- Trailing zeros are significant and required

### Temporal Types

```odin
effective = date                   ; date only (YYYY-MM-DD)
expires = !date                    ; required date
created = timestamp                ; full timestamp (ISO 8601)
start_time = time                  ; time only (THH:MM:SS)
term = duration                    ; ISO 8601 duration (P6M)
```

### Default Values

Default values appear after the type and constraints:

```odin
status = ("draft", "published") "draft"  ; enum with default
priority = ##:(1..5) ##3                 ; integer with default
rate = #:(0..1) #0.05                    ; number with default
enabled = ? ?true                        ; boolean with default
country = :(2) "US"                      ; string with default
```

**Default Value Rules:**
1. **Type-prefixed** — Default value uses same type prefix as field
2. **Within constraints** — Default must satisfy field constraints
3. **Optional fields only** — Required (`!`) fields cannot have defaults

### Reference

Use `@` to reference other paths.

```odin
parent = @                         ; reference to any path
manager = !@                       ; required reference
billing = @addresses               ; reference to addresses array
primary = @contacts[0]             ; reference to first contact
```

### Binary

Use `^` for binary data with optional algorithm constraint.

```odin
data = ^                           ; any binary
signature = !^                     ; required binary
hash = ^sha256                     ; SHA-256 hash
photo = ^:(..1048576)              ; max 1MB
```

### Null

Use `~` to indicate nullable fields.

```odin
nickname = ~                       ; nullable string
notes = ~:(..1000)                 ; nullable, max 1000 chars
```

### Union Types

Use `|` to chain multiple types. `|""` adds string, `|~` adds null.

```odin
; Basic unions
value = #|""                       ; number or string
id = ##|""                         ; integer or string

; Multi-type unions
flexible = #|?                     ; number or boolean
choice = #|?|""                    ; number or boolean or string
temporal = date|timestamp          ; date or timestamp

; Nullable unions
nullable_num = ~#                  ; nullable number (modifier)
num_or_null = #|~                  ; number or null (union)
tri_state = ?|~                    ; boolean or null
everything = #|?|~|""              ; number or boolean or null or string
```

**Union Rules:**
- `|` separates types in a union
- `|""` adds string as final option
- `~` after `|` means null type (not nullable modifier)
- `~` as prefix means nullable (modifier)
- Validators try types in declaration order

---

## Constraints

The colon `:` introduces constraints.

### String Constraints

```odin
; Length bounds
code = :(3)                        ; exactly 3 characters
code = :(3..)                      ; minimum 3
code = :(..10)                     ; maximum 10
code = :(3..10)                    ; 3 to 10

; Pattern (first char after : is delimiter)
email = :/^[^@]+@[^@]+\.[^@]+$/
url = :|^https?://[^\s]+|         ; pipe delimiter

; Enumeration
status = (pending, active, closed)
tier = ("bronze", "silver", "gold") "silver"  ; with default
```

### Numeric Constraints

```odin
age = ##:(0..150)                  ; integer 0-150
quantity = ##:(1..)                ; integer >= 1
discount = #:(0..1)                ; decimal 0-1
price = #$:(0..999999.99)
priority = ##:(1..5) ##3           ; with default
```

### Temporal Constraints

```odin
birth_date = date:(1900-01-01..2025-12-31)
effective = date:(2024-01-01..)    ; on or after
created = timestamp:(2024-01-01T00:00:00Z..)
```

### Binary Constraints

```odin
thumbnail = ^:(..102400)           ; max 100KB
hash = ^sha256:(32)                ; exactly 32 bytes
signature = ^ed25519:(64)          ; exactly 64 bytes
```

---

## EBNF Grammar (Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Type Specifiers *)
type_spec       = union_type | single_type ;
union_type      = single_type , { "|" , union_member } ;
union_member    = single_type | "~" | '""' ;

single_type     = bool_type | numeric_type | temporal_type
                | reference_type | binary_type | enum_type | type_ref ;

bool_type       = "?" ;
numeric_type    = "#" | "##" | "#" , "." , digits | "#$" , [ "." , digits ] | "#%" ;
temporal_type   = "date" | "timestamp" | "time" | "duration" ;
reference_type  = "@" , [ path ] ;
binary_type     = "^" , [ algorithm ] ;
enum_type       = "(" , enum_values , ")" ;

(* Constraints *)
constraint      = ":" , constraint_expr ;
constraint_expr = bounds | pattern | "unique" | format_constraint ;
bounds          = "(" , [ bound_min ] , ".." , [ bound_max ] , ")" ;
pattern         = delimiter , regex , delimiter ;
format_constraint = "format" , format_name ;

(* Default Value *)
default         = typed_value ;
enum_values     = value , { "," , value } ;
```

---

*See also: [Foundations](Foundations.md) | [Composition](Composition.md) | [Advanced](Advanced.md) | [Reference](Reference.md)*
