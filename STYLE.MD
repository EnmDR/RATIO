# Academic Document Style Specification

*Specification of a visual and structural style schema for academic documents in Microsoft Word*

| | |
|---|---|
| **Author** | Enmanuel Damas Reyes |
| **Version** | 1.0 |
| **Date** | 2026-03-21 |
| **Status** | Frozen |
| **Platform** | Microsoft Word |

## Scope

This specification defines two things: the visual properties of document elements (how they look) and the usage conventions that govern when and how each element is used (how content is organized into elements). Together, they constitute the complete presentational layer of an academic document.

This specification is a schema, not content. It defines property values and usage rules for every element type, but contains no document content. A concrete document that applies these values and rules is an **instantiation** of this specification. This specification does not govern linguistic style (sentence construction, tone, word choice).

## Definitions

**Base text style.** The root style from which all text elements inherit. Every property of every text element defaults to the base text style unless explicitly overridden. An element description that says "Base" for a property means that property is inherited from the base text style without modification.

**Override.** A property value that differs from the base text style. Element definitions list only overrides; all unlisted properties are inherited from the base. In comparative tables that display multiple elements side by side, inherited values are listed as "Base" to maintain row alignment across columns.

**Context-only property.** A property that exists only in specific element contexts (e.g., vertical alignment in table cells, marker format in lists). Context-only properties have no base value and must always be declared explicitly.

**Instantiation.** A concrete document that applies the property values and usage rules defined in this specification. An instantiation is valid if every element conforms to the visual properties in Part I and the usage conventions in Part II.

**Institutional typography.** Typography mandated by the university's visual identity. The font, weight, and size may vary across institutions. This specification references it by role, not by value. The current institutional font is Vitor.

---

# Part I — Visual properties

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
| Top border | None |
| Bottom border | None |
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

---

# Part II — Usage conventions

## Headings

Three heading levels below the document subtitle: heading 1 (main section), heading 2 (subdivision), heading 3 (specific unit). If complexity suggests a fourth level, it is integrated through prose within heading 3.

## Paragraphs

The paragraph is the default format. Each paragraph develops a single nuclear idea. Typical length is 2–5 sentences: one nuclear sentence followed by 1–4 factual development sentences.

## Lists

### Unordered lists

Unordered lists are used for sets of more than three discrete items without strict precedence that belong to the same conceptual level.

### Ordered lists

Ordered lists are used for sets of more than three items with strict precedence (temporal, causal, logical, procedural, or hierarchical).

### Inline enumerations

Inline enumerations are used for sets of two to four items with or without strict precedence.

### When not to use lists

Lists are not used when the relationship between items is what matters rather than the items themselves. If the content requires argumentative connection, causal explanation, or logical progression between elements, prose is used instead.

## Tables

Tables are used for sets of items classified simultaneously along two or more orthogonal dimensions. If the information has only one dimension, a list is used instead.

## Figures

Figures are used for structural, spatial, temporal, or causal relationships whose specification in prose would compromise intelligibility. Every figure carries a factual description that identifies what it shows; this description is adjacent text, not a formal Word caption. Every figure is referenced in the surrounding text. A figure without a text reference is not included.

## Scientific notation

Inline expressions (integrated in text) use Unicode. Block expressions use LaTeX. The criterion is syntactic: if the expression functions as an element within a sentence, it is inline (Unicode); if it constitutes an independent statement outside the textual flow, it is block (LaTeX).

## Typographic styles

Bold is used for key terms at first defining occurrence in the document, and in lists for the key term and the colon that follows it. After first occurrence, no typographic emphasis is applied.

Italic is used for unadapted foreign-language terms, non-hispanicized Latin expressions, binomial names of microorganisms, gene symbols (following the official nomenclature of the organism), titles of works, and metalinguistic uses. Protein symbols are not italicized — only gene symbols are.

Underline is not used. Strikethrough is not used.

Acronyms carry no special formatting beyond parentheses at first occurrence.

## Typographic signs

The hyphen (-) is used for lexical composition. The en dash (–) is used for numerical and positional ranges. The em dash (—) is not used; asides are resolved with commas or parentheses.

Double quotation marks ("") are the primary delimiter, following international biomedical convention rather than the RAE preference for angular quotation marks (« »). Single quotation marks ('') are the secondary delimiter. Parentheses are used for asides, complementary information, and acronyms or symbols at first occurrence. Brackets are used for interventions within quotations and clarifications within parentheses.

## Abbreviated forms

The full form is written at first occurrence followed by the abbreviated form in parentheses. From that point on, only the abbreviated form is used.

Abbreviations carry a terminal period. The abbreviation period is absorbed by the sentence-final period (only one period is written). It is not absorbed by other punctuation marks (colon, semicolon, comma).

## Numerical conventions

The decimal separator is the period. The thousands separator is the thin space. Numerals are used for experimental data, measurements, values with units, and quantities above ten. Words are used for general quantities of ten or below, except at the start of a sentence where words are always used regardless of magnitude.

## Citation

Vancouver style.
