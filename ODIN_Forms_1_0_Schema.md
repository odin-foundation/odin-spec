# ODIN Forms 1.0 Schema

**Declarative Form Definition for Print and Screen Rendering**

---

## Overview

ODIN Forms defines a schema for declarative form layouts that render faithfully to both print (PDF) and screen (HTML/CSS). A form definition is a standard ODIN document conforming to this schema—the same parser, the same syntax, the same tooling.

ODIN Forms is print-first: every element has absolute positioning on a fixed page. Screen rendering is a convenience layer, not the primary target. This makes it ideal for government forms, regulatory filings, and any context where paper output must match a canonical layout.

---

## License

**Specification:** Creative Commons Attribution 4.0 International (CC-BY 4.0)

**Reference Implementations:** Apache License 2.0

---

## Design Principles

1. **Print-first** — Fixed page dimensions, absolute coordinates
2. **Self-describing** — Element types in path structure
3. **HTML5/SVG naming** — Familiar attribute names reduce learning curve
4. **Bidirectional binding** — Forms bind to ODIN data documents
5. **Validation inline** — Field constraints live with field definitions
6. **Composable** — Reuse common element definitions
7. **Accessible** — Screen reader support for PDF (Tagged PDF) and HTML (ARIA)

---

## Design Philosophy

### Why Print-First?

Most form libraries optimize for responsive web layouts and treat PDF as an export afterthought. ODIN Forms inverts this: the canonical representation is a fixed page with absolute coordinates. When you know the output is print, everything simplifies:

- No responsive negotiation
- No flow layout
- No text reflow
- Fields are rectangles at coordinates

Screen rendering becomes straightforward: map the page to a viewport, scale uniformly, position elements absolutely. The web version is a faithful preview of the printed output.

### Why Absolute Positioning?

Government forms, insurance applications, and regulatory filings have precise layout requirements. Field 12a must be at exactly (2.5", 4.75") with dimensions (1.5" × 0.25"). Relative or flow layouts can't guarantee this.

Absolute positioning means:
- Pixel-perfect PDF output
- Form overlays on scanned documents
- Predictable field locations for OCR/scanning
- No layout engine variations between renderers

### Why Bind to ODIN?

A form is a UI over a data document. Field values must flow into a structured representation. ODIN Forms uses the `bind` attribute with ODIN path references (`@path.to.field`) to declare where each field's value belongs in the output document.

When a form is submitted:
1. Walk all fields
2. Validate against field constraints
3. Emit an ODIN document with values at bound paths

The form definition and data schema are separate but linked—the form knows where data goes, the schema knows what data is valid.

---

## Document Structure

An ODIN Forms document has these sections:

1. **Metadata** (`$`) — Document-level settings
2. **Localization** (`$.i18n`) — Multi-language labels (optional)
3. **Page defaults** (`$.page`) — Default page dimensions and margins
4. **Screen settings** (`$.screen`) — Screen rendering options (optional)
5. **Odincode** (`$.odincode`) — Self-digitizing barcode settings (optional)
6. **Pages** (`page[n]`) — Array of page definitions with elements

```odin
{$}
odin = "1.0.0"
forms = "1.0.0"
title = "Application for Insurance"
id = "form_auto_app_v1"
lang = "en"

{$.i18n}
en.field_ssn = "Social Security Number"
en.field_dob = "Date of Birth"
es.field_ssn = "Número de Seguro Social"
es.field_dob = "Fecha de Nacimiento"

{$.page}
width = #8.5
height = #11
unit = "inch"

{$.screen}
scale = #1.0

{$.odincode}
enabled = ?true
zone = "bottom-center"

{page[0]}
{.text.title}
; ... elements

{page[1]}
{.field.employer}
; ... elements
```

### Localization (i18n)

Localization is metadata for tooling, not core schema semantics. Store translations under `$.i18n` with language codes as path prefixes:

```odin
{$.i18n}
en.title = "Personal Auto Application"
en.section1 = "Applicant Information"
en.field_name = "Full Legal Name"
es.title = "Solicitud de Auto Personal"
es.section1 = "Información del Solicitante"
es.field_name = "Nombre Legal Completo"
```

Field labels can reference i18n keys:
```odin
{.field.name}
label = @$.i18n.en.field_name
```

Or tooling can resolve labels at render time based on `$.lang`. The form definition is agnostic—renderers interpret i18n however they want.

### Screen Settings

Optional settings for screen/web rendering:

| Property | Type | Description |
|----------|------|-------------|
| `$.screen.scale` | number | Default zoom factor (1.0 = 100%) |

### Odincode

The Odincode enables self-digitizing forms. When enabled, the renderer generates a PDF417 barcode containing the page's filled field data as raw ODIN text. See **ODIN Barcode 1.0** specification for payload format.

| Property | Type | Description |
|----------|------|-------------|
| `$.odincode.enabled` | boolean | Enable Odincode generation |
| `$.odincode.zone` | enum | Placement zone: `top-center` or `bottom-center` |

### Placement Zones

Odincode placement is restricted to two zones for reliable scanning and consistent form layout. The 1.5" height accommodates dual PDF417 barcodes (Macro mode) for larger payloads.

```
+--------------------------------------------------+
|                   0.25" margin                    |
+--------------------------------------------------+
|     +----- TOP-CENTER ODINCODE ZONE -----+       |
|     |     (up to 8" wide × 1.5" tall)    |       |
|     +------------------------------------+       |
|                                                  |
|                  FORM CONTENT                    |
|                                                  |
|     +------------------------------------+       |
|     |    BOTTOM-CENTER ODINCODE ZONE     |       |
|     |     (up to 8" wide × 1.5" tall)    |       |
+--------------------------------------------------+
|                   0.25" margin                    |
+--------------------------------------------------+
```

| Zone | Position | Description |
|------|----------|-------------|
| `top-center` | Top edge, horizontally centered | 0.25" from top edge |
| `bottom-center` | Bottom edge, horizontally centered | 0.25" from bottom edge |

### Zone Specifications

| Specification | Value |
|---------------|-------|
| Edge margin | 0.25" from page edge |
| Max height | 1.5" |
| Max width | Page width − 0.5" (e.g., 8.0" on 8.5" paper) |
| Anchoring | Barcode anchored to the 0.25" exclusion zone |

Renderers must reserve the Odincode zone — form elements should not overlap. The barcode width varies based on data payload, expanding up to the maximum allowed width. For large payloads exceeding single-barcode capacity, dual barcodes may be rendered side-by-side within this zone.

---

## Coordinate System

| Property | Description |
|----------|-------------|
| Origin | Top-left of page |
| X-axis | Left to right |
| Y-axis | Top to bottom |
| Unit | Defined in `$.page.unit` |

Supported units: `inch`, `cm`, `mm`, `pt` (72 per inch)

All coordinates and dimensions are in the document's declared unit.

---

## Reusable Type Definitions

### @position

Base positioning for all elements.

```odin
{@position}
x = !#
y = !#
```

### @dimensions

Width and height for rectangular elements.

```odin
{@dimensions}
w = !#
h = !#
```

### @stroke

Stroke properties for geometric elements.

```odin
{@stroke}
stroke = :/^#[0-9A-Fa-f]{6}$/
stroke-width = #:(0..)
stroke-opacity = #:(0..1)
stroke-dasharray =
stroke-linecap = (butt, round, square)
stroke-linejoin = (miter, round, bevel)
```

### @fill

Fill properties for closed shapes.

```odin
{@fill}
fill = :/^#[0-9A-Fa-f]{6}$|^none$/
fill-opacity = #:(0..1)
```

### @font

Typography properties for text elements.

```odin
{@font}
font-family = "Helvetica"
font-size = ##:(1..) ##12
font-weight = (normal, bold) normal
font-style = (normal, italic) normal
text-align = (left, center, right) left
color = :/^#[0-9A-Fa-f]{6}$/ #000000
```

### @validation

Field validation constraints.

```odin
{@validation}
required = ?
pattern =
minLength = ##:(0..)
maxLength = ##:(1..)
min =
max =
```

### @binding

Data binding for fields.

```odin
{@binding}
bind = !@
```

---

## Element Types

### Geometric Elements

#### line

A line segment between two points.

```odin
{@line}
x1 = !#
y1 = !#
x2 = !#
y2 = !#
= @stroke
```

**Example:**
```odin
{page[0]}
{.line.header_rule}
x1 = #0.5
y1 = #1
x2 = #8
y2 = #1
stroke = "#000000"
stroke-width = #0.5
```

#### rect

A rectangle.

```odin
{@rect}
= @position & @dimensions & @stroke & @fill
rx = #:(0..)
ry = #:(0..)
```

**Example:**
```odin
{.rect.section_box}
x = #0.5
y = #2
w = #7.5
h = #3
fill = "#f5f5f5"
stroke = "#cccccc"
rx = #0.1
ry = #0.1
```

#### circle

A circle defined by center and radius.

```odin
{@circle}
cx = !#
cy = !#
r = !#:(0..)
= @stroke & @fill
```

#### ellipse

An ellipse defined by center and radii.

```odin
{@ellipse}
cx = !#
cy = !#
rx = !#:(0..)
ry = !#:(0..)
= @stroke & @fill
```

#### polygon

A closed shape defined by points.

```odin
{@polygon}
points = !
= @stroke & @fill
```

Points format: `x1,y1 x2,y2 x3,y3 ...`

#### polyline

An open path defined by points.

```odin
{@polyline}
points = !
= @stroke
```

#### path

An SVG-style path.

```odin
{@path}
d = !
= @stroke & @fill
```

Path data uses SVG path syntax (M, L, C, Q, A, Z commands).

---

### Content Elements

#### text

Static text content.

```odin
{@text}
= @position & @font
content = !
rotate = #
```

**Example:**
```odin
{.text.section_header}
x = #0.5
y = #2.5
content = "Section 1: Applicant Information"
font-family = "Helvetica"
font-size = ##14
font-weight = "bold"
```

#### img

Embedded image.

```odin
{@img}
= @position & @dimensions
src = !^
alt = !
background = ?
```

The `src` field contains base64-encoded image data with format prefix. The `alt` field is required for accessibility (screen readers in both PDF and HTML).

When `background = ?true`, the image renders behind all other elements at the lowest z-index. Background images are non-interactive — they cannot be selected, moved, or resized in editing tools. This is used for scanned form templates where the image provides the visual structure and form fields are overlaid on top.

**Example:**
```odin
{.img.logo}
x = #0.5
y = #0.5
w = #1.5
h = #0.5
src = ^png:iVBORw0KGgoAAAANSUhEUgAA...
alt = "Company Logo"

{.img.page_template}
x = #0
y = #0
w = #8.5
h = #11
src = ^webp:UklGRgAAAA...
alt = "Page 1 template"
background = ?true
```

#### barcode

1D or 2D barcode.

```odin
{@barcode}
= @position & @dimensions
type = !(code39, code128, qr, datamatrix, pdf417)
content = !
alt = !
```

The `alt` field is required for accessibility—describes the barcode's purpose for screen readers.

**Example:**
```odin
{.barcode.document_id}
x = #7
y = #0.5
w = #1
h = #1
type = "qr"
content = "DOC-2024-001234"
alt = "Document tracking code DOC-2024-001234"
```

---

### Field Elements

All fields share common structure under `field.*` paths.

#### Common Field Properties

```odin
{@field_base}
= @position & @dimensions & @validation & @binding
type = !(text, checkbox, radio, select, multiselect, date, signature)
label = !
aria-label =
tabindex = ##:(0..)
readonly = ?
```

The `label` field is required and serves dual purpose: visible label for sighted users and accessibility label for screen readers. Use `aria-label` to override the screen reader text when it should differ from the visible label.

#### Field Values

ODIN Forms supports two modes for field data:

**Inline values (primary)** — Field values are stored directly on the field element. A single `.form.odin` file contains both layout and data, making it self-contained and portable.

```odin
{.field.name}
type = "text"
label = "Full Name"
value = "John Smith"
bind = @insured.name
```

**Bound data document** — The form definition contains only layout and bind paths. A separate ODIN data document provides the values. The renderer resolves bind paths (`@insured.name`) against the data document at render time.

When both are present, inline values take precedence over bound document values. Import implementations must support both modes. Export should produce inline values as the primary format.

#### Value Properties by Field Type

| Field Type | Value Property | Type | Description |
|------------|---------------|------|-------------|
| `text` | `value` | string | Current text content |
| `checkbox` | `checked` | boolean | Whether the checkbox is checked |
| `radio` | `value` | string | The option value this radio represents (group-level; the selected radio's `value` is the group's current value) |
| `select` | `selected` | string | Currently selected option value |
| `multiselect` | `selected` | string array | Currently selected option values |
| `date` | `value` | date | Current date value |
| `signature` | `value` | binary | Captured signature data |

**Accessibility notes:**
- PDF renderers must emit Tagged PDF structure (`/Form` tags) for screen reader support
- HTML renderers must include ARIA roles and labels on all interactive elements

#### field (type = text)

Text input field.

```odin
{page[0]}
{.field.ssn}
type = "text"
x = #1.5
y = #2.25
w = #2
h = #0.25
label = "Social Security Number"
value = "123-45-6789"
required = ?true
pattern = "^\d{3}-\d{2}-\d{4}$"
mask = "###-##-####"
bind = @insured.ssn
```

**Text-specific properties:**

| Property | Type | Description |
|----------|------|-------------|
| `value` | string | Current field value |
| `inputType` | enum | Screen rendering hint: `text` (default), `email`, `tel`, `password`, `number`, `url`. Print output is identical for all types — this controls the HTML5 `<input type>` attribute for screen rendering, enabling mobile keyboards, browser validation, and password masking. |
| `mask` | string | Input mask pattern |
| `placeholder` | string | Placeholder text |
| `multiline` | boolean | Allow multiple lines |
| `maxLines` | integer | Max lines if multiline |

#### field (type = checkbox)

Boolean checkbox.

```odin
{.field.agree_terms}
type = "checkbox"
x = #0.5
y = #8
w = #0.2
h = #0.2
label = "I agree to the terms and conditions"
checked = ?true
required = ?true
bind = @application.termsAccepted
```

#### field (type = radio)

Radio button (part of a group).

```odin
{.field.coverage_basic}
type = "radio"
x = #0.5
y = #4
w = #0.2
h = #0.2
label = "Basic Coverage"
group = "coverage"
value = "basic"
bind = @policy.coverageLevel

{.field.coverage_standard}
type = "radio"
x = #0.5
y = #4.3
w = #0.2
h = #0.2
label = "Standard Coverage"
group = "coverage"
value = "standard"
bind = @policy.coverageLevel

{.field.coverage_premium}
type = "radio"
x = #0.5
y = #4.6
w = #0.2
h = #0.2
label = "Premium Coverage"
group = "coverage"
value = "premium"
bind = @policy.coverageLevel
```

**Radio-specific properties:**

| Property | Type | Description |
|----------|------|-------------|
| `group` | string | Radio group name |
| `value` | string | Value when selected |

#### field (type = select)

Dropdown selection.

```odin
{.field.state}
type = "select"
x = #4
y = #3
w = #1.5
h = #0.3
label = "State"
selected = "TX"
required = ?true
bind = @insured.address.state

{.field.state.options[] : ~}
"AL"
"AK"
"AZ"
"AR"
"CA"
"CO"
"CT"
"FL"
"GA"
"NY"
"TX"
; ... remaining states
```

**Select-specific properties:**

| Property | Type | Description |
|----------|------|-------------|
| `options` | string array | Array of valid option values |
| `selected` | string | Currently selected option value |
| `placeholder` | string | Default unselected text |

#### field (type = multiselect)

Multiple selection list.

```odin
{.field.coverages}
type = "multiselect"
x = #0.5
y = #5
w = #3
h = #1.5
label = "Select Coverages"
required = ?true
minSelect = ##1
maxSelect = ##6
bind = @policy.selectedCoverages

{.field.coverages.options[] : ~}
"liability"
"collision"
"comprehensive"
"uninsured"
"medical"
"rental"
```

**Multiselect-specific properties:**

| Property | Type | Description |
|----------|------|-------------|
| `options` | string array | Array of valid option values |
| `selected` | string array | Currently selected option values |
| `minSelect` | integer | Minimum selections required |
| `maxSelect` | integer | Maximum selections allowed |

#### field (type = date)

Date input.

```odin
{.field.dob}
type = "date"
x = #4
y = #2.25
w = #1.5
h = #0.25
label = "Date of Birth"
value = 1985-03-15
required = ?true
min = 1900-01-01
max = 2010-01-01
bind = @insured.birthDate
```

#### field (type = signature)

Signature capture area.

```odin
{.field.applicant_signature}
type = "signature"
x = #1
y = #9.5
w = #3
h = #0.75
label = "Applicant Signature"
required = ?true
bind = @signatures.applicant
```

**Signature-specific properties:**

| Property | Type | Description |
|----------|------|-------------|
| `date_field` | reference | Associated date field |

---

### Regions

Regions group repeating content with overflow handling. When bound to an array, child elements repeat for each item. When items exceed `max`, overflow pages are generated.

#### region

A container for repeating or overflowing content.

```odin
{@region}
= @position & @dimensions
bind = @
max = ##
overflow = 
```

| Property | Type | Description |
|----------|------|-------------|
| `bind` | reference | Path to array data source |
| `max` | integer | Maximum items before overflow |
| `overflow` | string | `clone` or template reference |

**Example: Driver list with overflow**

```odin
{page[1]}

{.text.section_header}
x = #0.5
y = #1
content = "Section 3: Driver Information"
font-size = ##14
font-weight = "bold"

{.region.drivers}
x = #0.5
y = #1.5
w = #7.5
h = #6
bind = @policy.drivers
max = ##4
overflow = "clone"

{.region.drivers.field.name}
x = #0
y = #0
y-offset = #1.2
w = #3
h = #0.3
label = "Driver Name"
bind = @.name

{.region.drivers.field.license}
x = #3.2
y = #0
y-offset = #1.2
w = #2
h = #0.3
label = "License Number"
bind = @.licenseNumber
```

#### Region Child Positioning

Child elements use coordinates relative to the region's origin. The `y-offset` property specifies vertical spacing between repeated items.

| Property | Type | Description |
|----------|------|-------------|
| `y-offset` | number | Vertical spacing between items |
| `x-offset` | number | Horizontal spacing (for horizontal layouts) |

Child positions resolve as:
- Item 0: `(region.x + child.x, region.y + child.y)`
- Item 1: `(region.x + child.x, region.y + child.y + y-offset)`
- Item N: `(region.x + child.x, region.y + child.y + (N × y-offset))`

#### Region Binding

When a region binds to an array path, child elements use `@.` for relative binding:

```odin
{.region.drivers}
bind = @policy.drivers        ; array of driver objects

{.region.drivers.field.name}
bind = @.name                 ; resolves to @policy.drivers[n].name

{.region.drivers.field.dob}
bind = @.dateOfBirth          ; resolves to @policy.drivers[n].dateOfBirth
```

The `@.` syntax means "current item in the bound array." This is implicit scoping — no additional attribute needed.

#### Overflow Behavior

When bound data exceeds `max` items, the `overflow` attribute controls behavior:

**`overflow = clone`**

Duplicates the entire page for additional items. Header, footer, static elements all repeat. The region continues with items N+1 through 2N, then 2N+1 through 3N, etc.

```odin
{.region.drivers}
bind = @policy.drivers
max = ##4
overflow = "clone"              ; 8 drivers = 2 pages
```

**`overflow = @template_name`**

Uses a page template for continuation pages. Allows different headers, footers, or layouts on continuation pages.

```odin
{.region.drivers}
bind = @policy.drivers
max = ##4
overflow = @tpl_drivers_continued
```

---

### Page Templates

Page templates define layouts for dynamically generated pages. They are not rendered directly — they're instantiated when overflow occurs.

#### Template Definition

Templates use `{@tpl_*}` naming convention:

```odin
{@tpl_drivers_continued}
page-template = ?true
continues = "region.drivers"
form-id = "ACORD 125 (Cont)"

{.text.header}
x = #0.5
y = #0.5
content = "Additional Drivers (Continued) — Page {@odin.page} of {@odin.total_pages}"
font-size = ##14
font-weight = "bold"

{.region.drivers}
x = #0.5
y = #1
w = #7.5
h = #8
max = ##6
overflow = @tpl_drivers_continued   ; recursive for unlimited pages
```

| Property | Type | Description |
|----------|------|-------------|
| `page-template` | boolean | Marks this as a template, not a concrete page |
| `continues` | string | Names the region this template continues |
| `form-id` | string | Form identifier for continuation pages |

#### Template Recursion

Templates can reference themselves for unlimited overflow:

```odin
{@tpl_transactions}
page-template = ?true
continues = "region.transactions"
overflow = @tpl_transactions      ; handles 100+ transactions
```

#### Template vs. Concrete Pages

| Aspect | Concrete Page | Page Template |
|--------|---------------|---------------|
| Header | `{page[0]}` | `{@tpl_name}` |
| Rendered | Always | Only on overflow |
| In page count | Yes | Dynamic |
| Contains | Static content | May include `{@odin.*}` variables |

---

### Render-Time Variables

Variables in the `@odin` namespace are resolved at render time. Use them in text content via interpolation.

#### Available Variables

| Variable | Type | Description |
|----------|------|-------------|
| `@odin.page` | integer | Current page number (1-based) |
| `@odin.total_pages` | integer | Total pages after overflow calculation |

#### Interpolation Syntax

Variables appear inside `{` `}` within string content:

```odin
{.text.page_number}
content = "Page {@odin.page} of {@odin.total_pages}"

{.text.footer}
content = "Form PA-100 — {@odin.page}"
```

Interpolation works in any string property: `content`, `label`, `alt`, `aria-label`.

#### Static vs. Dynamic Page Numbers

On concrete pages, `@odin.page` resolves to the page's fixed index + 1:
- `{page[0]}` → `@odin.page` = 1
- `{page[1]}` → `@odin.page` = 2

On template-generated pages, it resolves to the actual position in output:
- First overflow → `@odin.page` = 3 (if primary was page 2)
- Second overflow → `@odin.page` = 4

`@odin.total_pages` is only known after all overflow is calculated. Renderers must make two passes: first to determine overflow, second to render with final counts.

---

## Complete Example

```odin
{$}
odin = "1.0.0"
forms = "1.0.0"
title = "Personal Auto Application"
id = "form_pa_app_v1"
version = "1.0.0"

{$.page}
width = #8.5
height = #11
unit = "inch"
margin.top = #0.5
margin.right = #0.5
margin.bottom = #0.5
margin.left = #0.5

; --- Page 1: Applicant Information ---

{page[0]}

{.text.header}
x = #0.5
y = #0.5
content = "Personal Auto Insurance Application"
font-size = ##18
font-weight = "bold"

{.line.header_rule}
x1 = #0.5
y1 = #0.75
x2 = #8
y2 = #0.75
stroke = "#000000"
stroke-width = #1

{.text.section1}
x = #0.5
y = #1.1
content = "Section 1: Applicant Information"
font-size = ##12
font-weight = "bold"

{.rect.section1_box}
x = #0.5
y = #1.25
w = #7.5
h = #2
fill = "none"
stroke = "#999999"

{.text.label_name}
x = #0.6
y = #1.45
content = "Full Legal Name"
font-size = ##8
color = "#666666"

{.field.name}
type = "text"
x = #0.6
y = #1.55
w = #3.5
h = #0.3
label = "Full Legal Name"
required = ?true
minLength = ##1
maxLength = ##100
bind = @insured.name.full

{.text.label_ssn}
x = #4.3
y = #1.45
content = "Social Security Number"
font-size = ##8
color = "#666666"

{.field.ssn}
type = "text"
x = #4.3
y = #1.55
w = #2
h = #0.3
label = "Social Security Number"
required = ?true
pattern = "^\d{3}-\d{2}-\d{4}$"
mask = "###-##-####"
bind = @insured.ssn

{.text.label_dob}
x = #0.6
y = #2.05
content = "Date of Birth"
font-size = ##8
color = "#666666"

{.field.dob}
type = "date"
x = #0.6
y = #2.15
w = #1.5
h = #0.3
label = "Date of Birth"
required = ?true
min = 1900-01-01
max = 2010-01-01
bind = @insured.birthDate

{.text.label_phone}
x = #2.3
y = #2.05
content = "Phone Number"
font-size = ##8
color = "#666666"

{.field.phone}
type = "text"
x = #2.3
y = #2.15
w = #2
h = #0.3
label = "Phone Number"
required = ?true
pattern = "^\d{3}-\d{3}-\d{4}$"
mask = "(###) ###-####"
bind = @insured.phone

; --- Page 2: Vehicle Information ---

{page[1]}

{.text.header}
x = #0.5
y = #0.5
content = "Vehicle Information — Page {@odin.page} of {@odin.total_pages}"
font-size = ##14
font-weight = "bold"

{.text.vehicle_instructions}
x = #0.5
y = #0.8
content = "List all vehicles to be insured. If more than 3 vehicles, additional pages will be generated."
font-size = ##9
color = "#666666"

{.region.vehicles}
x = #0.5
y = #1.2
w = #7.5
h = #6
bind = @policy.vehicles
max = ##3
overflow = @tpl_vehicles_continued

{.region.vehicles.text.label_vin}
x = #0
y = #0
y-offset = #1.8
content = "Vehicle Identification Number (VIN)"
font-size = ##8
color = "#666666"

{.region.vehicles.field.vin}
x = #0
y = #0.15
y-offset = #1.8
w = #4
h = #0.3
label = "VIN"
required = ?true
minLength = ##17
maxLength = ##17
pattern = "^[A-HJ-NPR-Z0-9]{17}$"
bind = @.vin

{.region.vehicles.text.label_year}
x = #0
y = #0.55
y-offset = #1.8
content = "Year"
font-size = ##8
color = "#666666"

{.region.vehicles.field.year}
x = #0
y = #0.7
y-offset = #1.8
w = #1
h = #0.3
label = "Year"
required = ?true
pattern = "^\d{4}$"
bind = @.year

{.region.vehicles.text.label_make}
x = #1.2
y = #0.55
y-offset = #1.8
content = "Make"
font-size = ##8
color = "#666666"

{.region.vehicles.field.make}
x = #1.2
y = #0.7
y-offset = #1.8
w = #2
h = #0.3
label = "Make"
required = ?true
bind = @.make

{.region.vehicles.text.label_model}
x = #3.4
y = #0.55
y-offset = #1.8
content = "Model"
font-size = ##8
color = "#666666"

{.region.vehicles.field.model}
x = #3.4
y = #0.7
y-offset = #1.8
w = #2
h = #0.3
label = "Model"
required = ?true
bind = @.model

; --- Signature Section ---

{.line.sig_rule}
x1 = #0.5
y1 = #9
x2 = #8
y2 = #9
stroke = "#000000"

{.text.sig_instruction}
x = #0.5
y = #9.2
content = "By signing below, I certify that all information is true and accurate."
font-size = ##10

{.text.label_sig}
x = #0.6
y = #9.5
content = "Applicant Signature"
font-size = ##8
color = "#666666"

{.field.signature}
type = "signature"
x = #0.6
y = #9.6
w = #3
h = #0.6
label = "Applicant Signature"
required = ?true
bind = @signatures.applicant.image

{.text.label_sig_date}
x = #4
y = #9.5
content = "Date"
font-size = ##8
color = "#666666"

{.field.sig_date}
type = "date"
x = #4
y = #9.6
w = #1.5
h = #0.3
label = "Signature Date"
required = ?true
bind = @signatures.applicant.date

; --- Page Template: Vehicle Continuation ---

{@tpl_vehicles_continued}
page-template = ?true
continues = "region.vehicles"
form-id = "PA-APP (Cont)"

{.text.header}
x = #0.5
y = #0.5
content = "Additional Vehicles (Continued) — Page {@odin.page} of {@odin.total_pages}"
font-size = ##14
font-weight = "bold"

{.region.vehicles}
x = #0.5
y = #1
w = #7.5
h = #8
max = ##4
overflow = @tpl_vehicles_continued

{.region.vehicles.text.label_vin}
x = #0
y = #0
y-offset = #1.8
content = "VIN"
font-size = ##8
color = "#666666"

{.region.vehicles.field.vin}
x = #0
y = #0.15
y-offset = #1.8
w = #4
h = #0.3
label = "VIN"
required = ?true
bind = @.vin

{.region.vehicles.text.label_year}
x = #0
y = #0.55
y-offset = #1.8
content = "Year"
font-size = ##8
color = "#666666"

{.region.vehicles.field.year}
x = #0
y = #0.7
y-offset = #1.8
w = #1
h = #0.3
label = "Year"
required = ?true
bind = @.year

{.region.vehicles.text.label_make}
x = #1.2
y = #0.55
y-offset = #1.8
content = "Make"
font-size = ##8
color = "#666666"

{.region.vehicles.field.make}
x = #1.2
y = #0.7
y-offset = #1.8
w = #2
h = #0.3
label = "Make"
required = ?true
bind = @.make

{.region.vehicles.text.label_model}
x = #3.4
y = #0.55
y-offset = #1.8
content = "Model"
font-size = ##8
color = "#666666"

{.region.vehicles.field.model}
x = #3.4
y = #0.7
y-offset = #1.8
w = #2
h = #0.3
label = "Model"
required = ?true
bind = @.model

{.text.footer}
x = #0.5
y = #10
content = "Continuation Sheet — Attach to Application"
font-size = ##8
color = "#666666"
```

---

## Output Binding

When a form is submitted, the renderer walks all `field.*` elements (including those in regions), validates their values, and emits an ODIN document.

Given the form above with user input (5 vehicles, generating 2 pages), output would be:

```odin
{$}
odin = "1.0.0"
source.form = "form_pa_app_v1"
source.version = "1.0.0"
created = "2025-12-07T14:30:00Z"

{insured}
{.name}
full = "John David Smith"

{}
ssn = "123-45-6789"
birthDate = 1985-03-15
phone = "512-555-1234"

{policy}
{.vehicles[0]}
vin = "1HGCM82633A004352"
year = ##2022
make = "Honda"
model = "Accord"

{.vehicles[1]}
vin = "5YJSA1E26MF123456"
year = ##2023
make = "Tesla"
model = "Model 3"

{.vehicles[2]}
vin = "WVWZZZ3CZWE123456"
year = ##2021
make = "Volkswagen"
model = "ID.4"

{.vehicles[3]}
vin = "1G1YY22G955123456"
year = ##2020
make = "Chevrolet"
model = "Corvette"

{.vehicles[4]}
vin = "JN1TBNT30Z0123456"
year = ##2024
make = "Nissan"
model = "Z"

{signatures}
{.applicant}
image = ^png:iVBORw0KGgo...
date = 2025-12-07
```

---

## Schema Definition (ODIN Schema Format)

```odin
@schema = "odin-forms/1.0"

{$}
odin = !:(5)
forms = !:(5)
title = !
id = !
version =
lang = :(2..5)

{$.i18n}
; Localization is freeform - tooling interprets as needed

{$.page}
width = !#:(0..)
height = !#:(0..)
unit = !(inch, cm, mm, pt)
margin.top = #:(0..)
margin.right = #:(0..)
margin.bottom = #:(0..)
margin.left = #:(0..)

{$.screen}
scale = #:(0.1..10) #1.0

; --- Reusable Types ---

{@position}
x = !#
y = !#

{@dimensions}
w = !#:(0..)
h = !#:(0..)

{@stroke}
stroke = :/^#[0-9A-Fa-f]{6}$/
stroke-width = #:(0..)
stroke-opacity = #:(0..1)
stroke-dasharray =
stroke-linecap = (butt, round, square)
stroke-linejoin = (miter, round, bevel)

{@fill}
fill = :/^#[0-9A-Fa-f]{6}$|^none$/
fill-opacity = #:(0..1)

{@font}
font-family =
font-size = ##:(1..)
font-weight = (normal, bold)
font-style = (normal, italic)
text-align = (left, center, right)
color = :/^#[0-9A-Fa-f]{6}$/

; --- Element Schemas ---

{page[]}
:(1..100)

{page[].line}
x1 = !#
y1 = !#
x2 = !#
y2 = !#
= @stroke

{page[].rect}
= @position & @dimensions & @stroke & @fill
rx = #:(0..)
ry = #:(0..)

{page[].circle}
cx = !#
cy = !#
r = !#:(0..)
= @stroke & @fill

{page[].ellipse}
cx = !#
cy = !#
rx = !#:(0..)
ry = !#:(0..)
= @stroke & @fill

{page[].polygon}
points = !
= @stroke & @fill

{page[].polyline}
points = !
= @stroke

{page[].path}
d = !
= @stroke & @fill

{page[].text}
= @position & @font
content = !
rotate = #

{page[].img}
= @position & @dimensions
src = !^
alt = !

{page[].barcode}
= @position & @dimensions
type = !(code39, code128, qr, datamatrix, pdf417)
content = !
alt = !

{page[].field}
= @position & @dimensions
type = !(text, checkbox, radio, select, multiselect, date, signature)
label = !
aria-label =
tabindex = ##:(0..)
readonly = ?
required = ?
pattern =
minLength = ##:(0..)
maxLength = ##:(1..)
min =
max =
mask =
placeholder =
multiline = ?
maxLines = ##:(1..)
group =
value =
options =
minSelect = ##:(0..)
maxSelect = ##:(1..)
date_field = @
bind = !@

{page[].region}
= @position & @dimensions
bind = @
max = ##:(1..)
overflow =

; Region child elements (relative positioning)
{page[].region.text}
= @position & @font
content = !
y-offset = #
x-offset = #

{page[].region.field}
= @position & @dimensions
type = !(text, checkbox, radio, select, multiselect, date, signature)
label = !
y-offset = #
x-offset = #
bind = @

; --- Page Templates ---

{@tpl_*}
page-template = !?
continues =
form-id =

; Templates contain same elements as pages
; Validated separately as they're not instantiated directly
```

---

## Rendering Targets

### PDF

Direct mapping to PDF primitives:
- `line` → PDF path with moveto/lineto
- `rect` → PDF rectangle operator
- `circle`/`ellipse` → Bézier approximation
- `text` → Positioned text with font
- `img` → XObject image with `/Alt` tag
- `barcode` → Rasterized image with `/Alt` tag
- `field` → AcroForm field (if editable) or flattened graphics
- `region` → Repeated field groups with calculated positions

**Overflow handling:**
When region data exceeds `max`, renderer generates additional pages using either clone mode (duplicate current page) or template mode (instantiate `{@tpl_*}`). Two-pass rendering required: first pass calculates total pages, second pass renders with final `@odin.total_pages`.

**Accessibility (PDF/UA):**
Renderers must emit Tagged PDF structure for screen reader support:
- Document structure tree with `/Document`, `/Part`, `/Form` tags
- `/Figure` tags with `/Alt` text for images and barcodes
- `/Form` tags for interactive fields with `/TU` (tooltip) from label
- Reading order defined by tag sequence

### HTML/CSS

Map to absolutely-positioned elements:
- Page → `<div>` with fixed dimensions
- Geometric elements → SVG or CSS
- Text → `<span>` with absolute positioning
- Fields → `<input>` elements with absolute positioning
- Scale factor applies uniformly for viewport fit

**Accessibility (ARIA):**
Renderers must include:
- `role` attributes on interactive elements
- `aria-label` or associated `<label>` for all fields
- `alt` attributes on `<img>` elements
- `aria-describedby` for field instructions/errors
- Logical tab order via `tabindex`

### Print CSS

For direct browser printing:
- Use `@page` rules for dimensions
- All elements absolutely positioned
- Fields rendered as form controls or print-flattened

---

## Future Considerations

### Multiple Overflow Regions

v1.0 supports single-region overflow per page. When a page has multiple regions (e.g., drivers and vehicles), only one can overflow. Future enhancement:

```odin
{.region.drivers}
bind = @policy.drivers
max = ##4
overflow = @tpl_drivers_cont
overflow-priority = ##1

{.region.vehicles}
bind = @policy.vehicles
max = ##3
overflow = @tpl_vehicles_cont
overflow-priority = ##2
```

`overflow-priority` dictates which region overflows first. Lower numbers process first.

### Cross-Page Validation

Validation that spans primary and continuation pages:

```odin
{.region.drivers}
validate-cross-page = ?true    ; Require at least one primary driver across all pages
```

Deferred because it requires post-render validation on the bound document rather than form-time validation.

### Additional Render Variables

| Variable | Description |
|----------|-------------|
| `@odin.overflow_count` | Number of overflow pages for current region |
| `@odin.region_page` | Page number within region (1 = primary, 2+ = overflow) |

### PDF Bookmark Configuration

```odin
{$.pdf}
bookmark-template = "Page {@odin.page} ({@odin.continues})"
```

Auto-generates PDF outline/bookmarks for generated pages.

### Nested Regions

Regions within regions for hierarchical data:

```odin
{.region.vehicles}
bind = @policy.vehicles

{.region.vehicles.region.drivers}
bind = @.drivers    ; Drivers per vehicle
```

Deferred due to complexity in overflow calculation and coordinate resolution.

### Dynamic Column Layouts

Horizontal overflow for wide data:

```odin
{.region.transactions}
layout = columns
columns = ##3
overflow = @tpl_transactions_cont
```

### Conditional Template Selection

Template selection based on data characteristics:

```odin
{.region.claims}
overflow = @tpl_claims_simple:if claims.count < 10
overflow = @tpl_claims_detailed:if claims.count >= 10
```

### Reference: XFA Learnings

Adobe's XFA (XML Forms Architecture) solved similar problems before deprecation.

**Borrowed concepts:**
- Overflow target — explicit declaration of where excess goes
- Repeatable subform instances — our region binding
- Template vs. instance distinction

**Intentionally avoided:**
- XML verbosity — ODIN stays flat
- Implicit behaviors — everything explicit
- Runtime scripting — declarative only

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.1 | 2025-12 | Increased Odincode zone height to 1.5" to accommodate Macro PDF417 dual barcodes |
| 1.0.0 | 2025-12 | Initial specification: geometric elements, content elements, field types, regions, page templates, overflow handling, render-time variables |

---

**End of Specification**
