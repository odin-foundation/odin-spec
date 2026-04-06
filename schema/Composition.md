# ODIN Schema 1.0: Composition

<!-- LLM:
PURPOSE: Modifiers, arrays, and reusable type definitions
MODIFIERS: !=required ~=nullable *=confidential -=deprecated (order: !-*~)
ARRAYS: {path[]} with :(min..max) :unique; tabular: {arr[] : col1, col2}
REUSABLE: {@name} defines type, @name references it, @a & @b = intersection
OVERRIDE: = @base :override then redefine fields with tighter constraints
NAMESPACE: {@&com.company.type} for organization-specific types
SEE ALSO: Foundations.md Types.md Advanced.md Reference.md
-->

---

## Modifiers

### Modifier Order

```
field = [!][-][*][~][type][:[constraint]] [default]
```

### Examples

```odin
; Required
name = !                           ; required string
id = !##                           ; required integer

; Nullable
notes = ~                          ; nullable string
middle = ~:(..50)                  ; nullable, max 50 chars

; Confidential (sensitive data)
ssn = *:/^\d{3}-\d{2}-\d{4}$/      ; confidential, pattern
password = !*                      ; required, confidential

; Deprecated
legacy_id = -                      ; deprecated field

; Combinations
ssn = !*:/^\d{3}-\d{2}-\d{4}$/     ; required + confidential + pattern
tax_id = ~*                        ; nullable + confidential
```

---

## Arrays

### Array Definition

Use `[]` suffix in header to define array schema.

```odin
{items[]}                          ; array definition
sku = !
name = !
price = !#$
quantity = !##:(1..) ##1
```

### Array Constraints

Array bounds appear as the first line after the header.

```odin
{items[]}
:(1..100)                          ; 1 to 100 items
sku = !
name = !

{tags[]}
:(..10)                            ; max 10 tags
value = !

{required_fields[]}
:(1..)                             ; at least 1 item
name = !
```

### Unique Items

Use `:unique` for arrays with no duplicate values.

```odin
{emails[]}
:unique                            ; no duplicate emails
value = !:/^[^@]+@[^@]+$/

{codes[]}
:(1..10):unique                    ; 1-10 unique codes
value = !
```

### Tabular Schema Definition

For arrays containing flat objects, use tabular column syntax:

```odin
{line_items[] : sku, description, qty, price}
sku = !
description = !:(..200)
qty = !##:(1..)
price = !#$:(0..)
```

**Tabular Schema Rules:**
1. **Primitives only** — All column paths must resolve to primitive types
2. **Single-level nesting** — Column names may use one dot (`product.name`) or one index (`permissions[0]`)
3. **No multi-level nesting** — Paths like `a.b.c` or `a[0].b` not allowed
4. **Column order** — Column list defines serialization order

```odin
; ✓ VALID - all primitive fields
{contacts[] : name, email, phone}
name = !:(1..100)
email = !:/^[^@]+@[^@]+$/
phone = :/^\d{3}-\d{3}-\d{4}$/

; ✗ INVALID - nested object type reference
{customers[] : name, address}
address = @address                 ; not allowed in tabular
```

---

## Reusable Definitions

Define reusable types with `{@name}` syntax.

### Type Definition

```odin
{@address}
line1 = !:(1..100)
line2 = :(..100)
city = !:(1..50)
state = !:(2)
zip = !:/^\d{5}$/
country = :(2) "US"

{@money}
amount = !#$:(0..999999.99)
currency = :(3) "USD"
```

### Type Usage

Reference defined types with `@typename`.

```odin
{customer}
name = !
billing = @address                 ; uses @address definition
shipping = @address

{order}
total = @money
tax = @money
```

### Type Intersection

Combine multiple type definitions with `&`.

```odin
{@timestamps}
created = !timestamp
updated = timestamp

{@auditable}
created_by = !
updated_by =

; Compose types with &
{user}
= @base_entity & @timestamps & @auditable
email = !:/^[^@]+@[^@]+$/
role = !(admin, user, guest)
```

**Intersection Rules:**
- `&` combines all fields from referenced types
- Field conflicts (same name, different definition) are errors
- Order doesn't matter: `@a & @b` equals `@b & @a`
- Can mix with local field definitions

### Type Inheritance with Override

Use `:override` to inherit from a base type while overriding specific field constraints.

```odin
@import "./coverage.schema.odin" as base

{@tx_liability}
= @base.personal_auto_liability :override

{.bi}
per_person = !#$:(30000..)           ; TX minimum $30K
per_accident = !#$:(60000..)         ; TX minimum $60K
```

**Override Rules:**

| Aspect | Allowed Override |
|--------|-----------------|
| Constraints | More restrictive (narrower range) |
| Required | `optional` → `required` (not reverse) |
| Nullable | Remove nullability (not add) |
| Type | Must be same base type |

### Namespaced Custom Types

Use reverse-DNS notation for organization-specific types.

```odin
; Define namespaced types
{@&com.acme.customer_id}
value = !:/^ACME-\d{8}$/

; Use namespaced types
{order}
customer = @&com.acme.customer_id

; Extension fields using namespace
{claim}
id = !
&com.carrier.adjuster_code = :(6)
&com.carrier.priority_score = ##:(1..10)
```

**Namespace Rules:**
- Use reverse-DNS: `@&com.company.typename`
- Prevents conflicts between organizations
- Same syntax for type definitions and field extensions

---

## EBNF Grammar (Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Modifiers *)
modifiers       = { modifier } ;
modifier        = "!" | "~" | "*" | "-" ;

(* Headers *)
type_def_header = "@" , [ namespace ] , identifier ;
namespace       = "&" , reverse_dns , "." ;
array_header    = path , "[" , "]" ;
tabular_array_header = path , "[" , "]" , ":" , column_list ;
column_list     = identifier , { "," , identifier } ;

(* Type Usage and Intersection *)
type_ref        = "@" , [ namespace ] , identifier ;
intersection    = type_ref , { "&" , type_ref } , [ ":override" ] ;

(* Array Constraints *)
array_constraint = ":" , bounds , [ ":unique" ] | ":unique" ;
```

---

*See also: [Foundations](Foundations.md) | [Types](Types.md) | [Advanced](Advanced.md) | [Reference](Reference.md)*
