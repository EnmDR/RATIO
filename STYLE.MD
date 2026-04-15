# Academic Document Style Specification

*Specification of a visual style schema for academic documents in Microsoft Word*

| | |
|---|---|
| **Author** | Enmanuel Damas Reyes |
| **Version** | 1.0 |
| **Date** | 2026-03-21 |
| **Status** | Frozen |
| **Platform** | Microsoft Word |

## Scope

This specification defines the visual properties of document elements: the typographic, chromatic, and spatial values that determine how each element looks. Together, they constitute the complete presentational layer of an academic document.

This specification is a schema, not content. It defines property values for every element type, but contains no document content. A concrete document that applies these values is an **instantiation** of this specification. This specification does not govern linguistic style or usage conventions.

## Definitions

**Base text style.** The root style from which all text elements inherit. Every property of every text element defaults to the base text style unless explicitly overridden. An element description that says "Base" for a property means that property is inherited from the base text style without modification.

**Override.** A property value that differs from the base text style. Element definitions list only overrides; all unlisted properties are inherited from the base. In comparative tables that display multiple elements side by side, inherited values are listed as "Base" to maintain row alignment across columns.

**Context-only property.** A property that exists only in specific element contexts (e.g., vertical alignment in table cells, marker format in lists). Context-only properties have no base value and must always be declared explicitly.

**Instantiation.** A concrete document that applies the property values defined in this specification. An instantiation is valid if every element conforms to the visual properties defined herein.

**Institutional typography.** Typography mandated by the university's visual identity. The font, weight, and size may vary across institutions. This specification references it by role, not by value. The current institutional font is Vitor.

## Page layout

| Property | Value |
|----------|-------|
| Page size | A4 (210 × 297 mm) |
| Orientation | Portrait |
| Top margin | 2.5 cm |
| Bottom margin | 2.2 cm |
| Left margin | 2.5 cm |
| Right margin | 2.5 cm |

## Color system

Colors are defined by semantic role. Concrete color values are assigned per instantiation.

### Color roles

| Role | Assigned to |
|------|-------------|
| Primary | Document title, document subtitle, first-page footer university name, subsequent-page header, unordered list marker (level 1), highlight block borders, table borders |
| Secondary | Heading 2, heading 3, table title, table header text |
| Tertiary | Heading 1 |
| Quaternary | Even-row background in table body |
| Neutral | Body text, first-page footer subject name, subsequent-page footer (author name, page number), unordered list markers (levels 2–3) |
| White | Backgrounds where no quaternary fill applies |

## Base text style

All text elements inherit from this style. Element definitions below specify only overrides.

| Property | Value |
|----------|-------|
| Font | Calibri |
| Weight | Normal |
| Style | Regular |
| Size | 11 pt |
| Capitalization | Sentence case |
| Line height | 1.15 |
| Alignment | Justified |
| Space before | 0 pt |
| Space after | 6 pt |
| Indent | None |
| Color | Neutral |

## Heading hierarchy

No heading uses numbering. No heading uses terminal punctuation. All headings use the base line height.

| Property | Document title | Document subtitle | Heading 1 | Heading 2 | Heading 3 |
|----------|---------------|-------------------|-----------|-----------|-----------|
| Font | Aptos Display | Aptos Display | Aptos Display | Aptos Display | Aptos Display |
| Weight | Bold | Base | Bold | Base | Base |
| Style | Base | Base | Base | Base | Italic |
| Size | 18 pt | 15 pt | 14 pt | 14 pt | Base |
| Color | Primary | Primary | Tertiary | Secondary | Secondary |
| Alignment | Left | Left | Left | Left | Left |
| Space before | 0 pt | Base | 18 pt | 12 pt | 8 pt |
| Space after | 12 pt | Base | 6 pt | 4 pt | 2 pt |

## Page furniture

All page furniture overrides space after to 0 pt. No element uses terminal punctuation.

### First-page header

| Property | Value |
|----------|-------|
| Font | Institutional typography |
| Size | 15 pt |
| Capitalization | Uppercase |
| Alignment | Right |

### First-page footer

Two elements on a single line, separated by a tab stop.

| Property | University name | Subject name |
|----------|----------------|--------------|
| Font | Institutional typography | Base |
| Color | Primary | Base |
| Alignment | Left | Left |

### Subsequent-page header

Two elements on a single line.

| Property | Document title | Document subtitle |
|----------|---------------|-------------------|
| Weight | Bold | Base |
| Style | Base | Italic |
| Color | Primary | Primary |
| Alignment | Left | Left |

### Subsequent-page footer

| Property | Author name | Page number |
|----------|-------------|-------------|
| Alignment | Left | Right |

## Lists

### Unordered lists

| Property | Value |
|----------|-------|
| Space before list | 6 pt |
| Space after list | 6 pt |
| Left indent | 0.63 cm |
| Marker indent | Automatic |
| Level 1 marker | • (color: primary) |
| Level 2 marker | – (color: neutral) |
| Level 3 marker | None (color: neutral) |
| Maximum nesting | 3 levels |
| Terminal punctuation | Yes |

### Ordered lists

| Property | Value |
|----------|-------|
| Space before list | 6 pt |
| Space after list | 6 pt |
| Left indent | 0.63 cm |
| Marker indent | Automatic |
| Level 1 marker | 1. |
| Level 2 marker | a) |
| Maximum nesting | 2 levels |
| Terminal punctuation | Yes |

### Inline enumerations

| Property | Value |
|----------|-------|
| Marker format | Arabic numerals in parentheses |
| Maximum items | 4 |

## Figures

| Property | Value |
|----------|-------|
| Alignment | Justified (fit to window) or centered |
| Space before | 6 pt |
| Space after | 6 pt |
| Title | None |
| Caption | None |
| Numbering | None |

## Highlight blocks

A bordered text block used for remarks, reminders, or emphasized content.

| Property | Value |
|----------|-------|
| Typography | Base |
| Left border | Yes (color: primary) |
| Right border | Yes (color: primary) |
| Top border | Yes (color: primary) |
| Bottom border | Yes (color: primary) |
| Space before block | 6 pt |
| Space after block | 6 pt |

## Tables

### Table layout

| Property | Value |
|----------|-------|
| Fit | Autofit to content, then autofit to window |
| Space before table | 6 pt |
| Space after table | 6 pt |
| Title | Yes (color: secondary) |
| Caption | Yes |
| Numbering | None |

### Table borders

| Border type | Value |
|-------------|-------|
| Top external horizontal | Yes (color: primary) |
| Bottom external horizontal | Yes (color: primary) |
| External vertical | None |
| Header separator | None |
| Internal horizontal | None |
| Internal vertical | None |

### Table header cells

| Property | Value |
|----------|-------|
| Font | Aptos Display |
| Weight | Bold |
| Color | Secondary |
| Vertical alignment | Center |
| Horizontal alignment | Left |
| Space before | 6 pt |
| Space after | 6 pt |
| Terminal punctuation | None |

### Table body cells

| Property | Value |
|----------|-------|
| Vertical alignment | Center |
| Horizontal alignment | Left |
| Even-row background | Quaternary |
| Terminal punctuation | None |

## Mathematical text

Separate base style. Does not inherit from the base text style.

| Property | Value |
|----------|-------|
| Font | Latin Modern Math |
| Weight | Normal |
| Style | Regular |
| Size | 11 pt |
| Capitalization | Mathematical conventions |
| Indent | None |
| Equation numbering | None |

### Inline math

| Property | Value |
|----------|-------|
| Line height | 1.15 |
| Alignment | Integrated in justified paragraph |

### Block math

| Property | Value |
|----------|-------|
| Line height | Automatic |
| Alignment | Centered |
| Space before | 6 pt |
| Space after | 6 pt |

## Code text

Separate base style. Does not inherit from the base text style.

| Property | Value |
|----------|-------|
| Font | JetBrains Mono |
| Weight | Normal |
| Style | Regular |
| Size | 10 pt |
| Capitalization | Language conventions |
| Line height | 1.15 |
| Alignment | Left |
| Space before | 6 pt |
| Space after | 6 pt |
| Indent | None |
