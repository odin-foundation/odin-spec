# ODIN Schema 1.0: Advanced

<!-- LLM:
PURPOSE: Conditionals, invariants, formats, metadata directives, cardinality
CONDITIONAL: :if field = value (required when true), :unless field = value (required when false)
INVARIANT: :invariant expr (cross-field: total = subtotal + tax)
FORMAT: :format email|url|uuid|phone|ssn|vin|iban etc.
METADATA: :computed (derived), :immutable (cannot change after creation)
CARDINALITY: :of (min..max) fields, :one_of, :exactly_one, :at_most_one
SEE ALSO: Foundations.md Types.md Composition.md Reference.md
-->

---

## Conditional Fields

Use `:if` to define fields that are required or present based on another field's value.

### Basic Conditionals

```odin
{payment}
method = !(card, bank, crypto)
card_number = !:if method = card
card_expiry = !:if method = card
routing_number = !:if method = bank
account_number = !:if method = bank
wallet_address = !:if method = crypto
```

### Multiple Conditions (OR)

```odin
{vehicle}
type = !(auto, motorcycle, boat)
vin = !:if type = auto
vin = !:if type = motorcycle       ; same field, multiple conditions (OR)
hull_id = !:if type = boat
```

### Conditional with Modifiers

```odin
{claim}
type = !(injury, property, liability)
injury_description = !:if type = injury           ; required if injury
medical_records = *:if type = injury              ; confidential if injury
property_value = #$:if type = property            ; currency if property
```

### Nested Conditionals

```odin
{policy}
coverage = !(basic, standard, premium)
deductible = !#$
roadside = ?:if coverage = standard
roadside = ?:if coverage = premium
rental_car = ?:if coverage = premium
rental_limit = #$:if rental_car = ?true            ; depends on another conditional
```

**Conditional Rules:**
- `:if field = value` — field is relevant when condition is true
- Multiple `:if` on same field act as OR
- Combine with `!` for "required when condition is true"
- Omit `!` for "optional when condition is true"

### Inverse Conditionals (:unless)

Use `:unless` for conditions where a field is required when a condition is FALSE.

```odin
{user}
email = !                              ; always required
password = !:unless sso_enabled        ; required if NOT using SSO

{order}
payment_method = !(card, invoice)
immediate_charge = ?:unless payment_method = invoice
```

`:unless field = value` is equivalent to `:if field != value` but reads more naturally.

**Conditional Scope:**

The `:if` condition can only reference fields within the **same header scope**:

```odin
{payment}
method = !(card, bank)
card_number = !:if method = card       ; ✓ 'method' is in same header

{order.details}
card_fee = #$:if method = card         ; ✗ cross-header reference not allowed
```

For cross-object conditions, use invariants instead.

---

## Invariants

Use `:invariant` under a header to define cross-field validation constraints.

### Basic Invariants

```odin
{order}
subtotal = !#$
tax = !#$
shipping = !#$
total = !#$
:invariant total = subtotal + tax + shipping

{date_range}
start = !date
end = !date
:invariant end >= start

{account}
balance = !#$
credit_limit = !#$
:invariant balance <= credit_limit
```

### Supported Operators

| Category | Operators |
|----------|-----------|
| Arithmetic | `+` `-` `*` `/` `%` |
| Comparison | `>` `<` `>=` `<=` `==` `!=` |
| Logic | `&&` `\|\|` `!` |
| Grouping | `(` `)` |

### Complex Invariants

```odin
{discount}
percentage = #:(0..100)
fixed_amount = #$:(0..)
subtotal = !#$
total = !#$
:invariant total >= 0
:invariant percentage == 0 || fixed_amount == 0
:invariant total = subtotal - (subtotal * percentage / 100) - fixed_amount

{inventory}
quantity = !##:(0..)
reserved = !##:(0..)
available = !##:(0..)
:invariant available = quantity - reserved
:invariant reserved <= quantity
```

**Invariant Rules:**
- Placed under header, applies to that object
- Multiple `:invariant` lines allowed per header
- References fields in current scope only
- No function calls or string operations
- Evaluated after all field validations pass

---

## Format Constraints

Use `:format` to validate string fields against common formats.

### General Formats

| Format | Description | Example |
|--------|-------------|---------|
| `email` | Email address | `user@example.com` |
| `url` | URL | `https://example.com/path` |
| `uri` | URI | `urn:isbn:0451450523` |
| `uuid` | UUID v4 | `550e8400-e29b-41d4-a716-446655440000` |
| `date` | ISO date | `2024-01-15` |
| `time` | ISO time | `14:30:00` |
| `datetime` | ISO datetime | `2024-01-15T14:30:00Z` |
| `hostname` | DNS hostname | `api.example.com` |
| `ipv4` | IPv4 address | `192.168.1.1` |
| `ipv6` | IPv6 address | `2001:db8::1` |
| `phone` | Phone (international) | `+1-555-123-4567` |
| `credit-card` | Credit card (13-19 digits) | `4111111111111111` |

### US Identifiers

| Format | Description | Example |
|--------|-------------|---------|
| `ssn` | Social Security Number | `123-45-6789` |
| `ein` | Employer ID Number | `12-3456789` |
| `zip` | ZIP code (5 or 9 digit) | `90210` or `90210-1234` |

### Vehicle & Financial

| Format | Description | Example |
|--------|-------------|---------|
| `vin` | Vehicle ID Number | `1HGBH41JXMN109186` |
| `iban` | International Bank Account | `DE89370400440532013000` |
| `bic` / `swift` | Bank Identifier Code | `DEUTDEFF` |
| `routing` | ABA Routing Number | `021000021` |
| `cusip` | US/Canada securities ID | `037833100` |
| `isin` | International Securities ID | `US0378331005` |
| `lei` | Legal Entity Identifier | `529900T8BM49AURSDO55` |

### Healthcare & Telecom

| Format | Description | Example |
|--------|-------------|---------|
| `npi` | National Provider ID | `1234567890` |
| `dea` | DEA Registration Number | `AB1234567` |
| `imei` | Mobile device IMEI | `353456789012345` |
| `iccid` | SIM card ICCID | `8901234567890123456` |

### Examples

```odin
{user}
email = !:format email              ; required, must be valid email
website = :format url               ; optional URL
id = !:format uuid                  ; required UUID

{payment}
card_number = *:format credit-card  ; confidential credit card

{employee}
ssn = !*:format ssn                 ; required, confidential SSN
```

---

## Field Metadata Directives

### Computed Fields (:computed)

Mark fields as derived values that are not provided in input data.

```odin
{order}
subtotal = !#$
tax = !#$
total = !#$:computed                 ; calculated from subtotal + tax
created_at = !timestamp:computed     ; system-generated timestamp
order_number = !:computed            ; auto-generated order ID
```

**Computed Field Rules:**
- Computed fields are excluded from input validation
- Used for generated values (IDs, timestamps, calculations)
- Can combine with `!` (required in output, computed by system)

**Implementation Note:** The `:computed` directive is metadata stored on the field definition. The SDK parses and preserves this metadata but does not generate computed values. Value generation is delegated to the application layer, which has access to the business logic and context needed for computation.

### Immutable Fields (:immutable)

Mark fields that cannot be changed after initial creation.

```odin
{user}
id = !:format uuid :immutable        ; cannot change after creation
email = !:format email               ; can be updated
created_at = !timestamp:immutable    ; creation timestamp, never changes

{transaction}
transaction_id = !:immutable
timestamp = !timestamp:immutable
amount = !#$:immutable               ; transaction amount is permanent
status = !(pending, completed, refunded)  ; status CAN change
```

**Immutable Field Rules:**
- First value assigned becomes permanent
- Update operations must not include immutable fields
- Commonly used for IDs, audit fields, transaction data

**Implementation Note:** The `:immutable` directive is metadata stored on the field definition. The SDK parses and preserves this metadata but does not enforce immutability at runtime. Enforcement is delegated to the application layer (e.g., ORM, API layer, database triggers), which manages the document lifecycle and can detect mutation attempts.

---

## Cardinality Constraints

Use `:of` to require N-of-M fields from a set.

### Syntax

```
:of (min..max) field1, field2, field3, ...
```

### Examples

```odin
{contact}
email =
phone =
address = @address
:of (1..) email, phone, address              ; at least one required

{identification}
ssn = *
passport = *
drivers_license = *
:of (1..1) ssn, passport, drivers_license    ; exactly one required

{notification}
email_opt_in = ?
sms_opt_in = ?
push_opt_in = ?
:of (..1) email_opt_in, sms_opt_in, push_opt_in  ; at most one
```

### Shorthand

| Shorthand | Equivalent | Meaning |
|-----------|------------|---------|
| `:one_of` | `:of (1..)` | At least one |
| `:exactly_one` | `:of (1..1)` | Exactly one |
| `:at_most_one` | `:of (..1)` | Zero or one |

```odin
{contact}
email =
phone =
:one_of email, phone                         ; at least one

{primary}
credit_card = @payment
bank_account = @payment
:exactly_one credit_card, bank_account       ; exactly one
```

**Cardinality Rules:**
- Placed under header, applies to that object
- Fields must be defined in same object
- A field "counts" if present and non-null
- Multiple `:of` constraints allowed per header

---

## Schema Metadata

Schema documents use the `$` header for metadata.

```odin
{$}
odin = "1.0.0"
schema = "1.0.0"
id = "com.example.order"
version = "2.1.0"
title = "Order Schema"
description = "Schema for e-commerce orders"
```

### File Naming Convention

Schema documents should use the `.schema.odin` extension:

```
auto-policy.schema.odin    ; Schema file
policy.odin                ; Data document
acord-to-odin.transform.odin  ; Transform
```

---

## EBNF Grammar (Fragment)

*See [Reference](Reference.md) for complete grammar.*

```ebnf
(* Conditional *)
conditional     = ":if" , path , operator , value
                | ":unless" , path , operator , value ;
operator        = "=" | "!=" | ">" | "<" | ">=" | "<=" ;

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

(* Format and Metadata Directives *)
format_directive = ":format" , format_name ;
computed_directive = ":computed" ;
immutable_directive = ":immutable" ;
```

---

*See also: [Foundations](Foundations.md) | [Types](Types.md) | [Composition](Composition.md) | [Reference](Reference.md)*
