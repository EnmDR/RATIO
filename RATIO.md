# RATIO

*Specification of an instructional configuration schema for Claude in claude.ai*

| | |
|---|---|
| **Author** | Enmanuel Damas Reyes |
| **Version** | 1.0 |
| **Date** | 2026-03-21 |
| **Status** | Frozen |
| **Platform** | claude.ai (Anthropic) |

## Scope

RATIO is an instructional design framework that organizes user-configurable instructions in claude.ai. It assigns each instruction to one of five layers—corresponding to the platform's five channels (user preferences, project instructions, style, skills, prompt)—and, within each layer, to a thematic facet that determines the type of content it admits.

RATIO is a schema, not content. It defines the structure, assignment rules, and drafting principles for instructions, but contains no instructions itself. A concrete set of instructions assigned to facets for a specific user, project, or task is an **instantiation** of RATIO. The framework is stable across instantiations; the content of each facet varies.

RATIO governs two things: where instructions are assigned (layers and facets) and how instructions are drafted (drafting principles). The drafting principles apply to instructions in all layers, including those within skill files. RATIO does not govern platform behavior (how the model resolves conflicts, how skills are triggered, how context is managed), the structural conventions of platform artifacts (the format of skill frontmatter, file organization within skill directories, the format of style presets), or the content of any particular instantiation.

## Motivation

Without an explicit architecture, instructions tend to be duplicated across channels, assigned to the wrong channel, or contradicted between layers. Duplication consumes context window without contributing new signal. Misassignment prevents an instruction from activating when it should, or keeps it active when it should not. Accidental contradiction introduces ambiguity that the model resolves non-deterministically.

RATIO prevents these conditions through two mechanisms: a decision tree that assigns each instruction to exactly one layer and exactly one facet, and a set of drafting principles that govern the linguistic form of instructions. This makes it possible to distinguish between accidental contradiction within a layer (a defect) and intentional overrides across layers (a design pattern made predictable by the platform's precedence behavior).

## Definitions

**Instruction.** The minimal unit of information that governs model behavior. Each instruction is assigned to exactly one facet. Each instruction takes one of three linguistic forms:
- Declarative: "X is Y."
- Policy: "When X, do Y" or "Do X in manner Y."
- Constraint: preferred form "Do Y instead of X." Negative form ("Do not do X") reserved for exclusions that cannot be reformulated positively.

**Layer.** A platform channel to which instructions are assigned. Each layer has a persistence (which conversations the instructions are effective in) and an activation (under what condition they enter the inference context).

**Facet.** A thematic category of instructions within a layer. Each facet has an obligation status (whether it requires content or may be left empty).

**Instantiation.** A concrete set of instructions populating the facets of one or more layers for a specific user, project, or task. An instantiation is valid if every instruction satisfies the assignment trees and drafting principles defined below.

## Drafting principles

The linguistic form of instructions affects how the model interprets them. These principles govern instruction drafting across all layers.

**Atomicity.** Each sentence contains a single instruction. Compound instructions are decomposed into independent sentences.

**Positive framing.** Instructions describe desired behavior rather than prohibited behavior. The form "Do Y instead of X" is preferred over "Do not do X." The negative form is reserved for exclusions that cannot be reformulated positively.

**Operational specificity.** Each instruction passes the unambiguous interpretation test: a reader without prior context would interpret it in exactly one way. Instructions tell the model what to do with information, not merely what information exists.

**Non-redundancy.** Each instruction appears in exactly one facet. Information already implied by an existing instruction is not repeated as a separate instruction.

**Format-content coherence.** The format in which instructions are written models the desired output format. If the desired output is prose, instructions are written in prose.

**Calibrated intensity.** With frontier models, standard declarative instructions suffice. Emphatic language (capitalization, obligation adverbs) is reserved for instructions the model tends to ignore, not used as a default.

## Layer architecture

### Layer table

Grounded: the layer architecture is determined by the platform and is not user-modifiable.

| Layer | Description | Persistence | Activation |
|-------|-------------|-------------|------------|
| L₁ or user preferences | Instructions that govern the model's baseline behavior across all interactions | All conversations | Always active |
| L₂ or project instructions | Instructions that govern the configuration of a specific project | Project conversations | Always active |
| L₃ or style | Instructions that govern the linguistic and pragmatic form of the output | Conversations with active style | Always active |
| L₄ or skills | Instructions that govern procedures contingent on a specific task type | All conversations | Conditionally active (semantic matching) |
| L₅ or prompt | Instructions that govern the execution of a specific task in a single message | Single message | One inference |

### Precedence

The platform resolves conflicts between layers by favoring the instruction with narrower persistence: L₅ overrides L₂, which overrides L₃, which overrides L₁. L₄ does not generate precedence conflicts because it activates conditionally and operates on task procedures rather than general behavior. This resolution order is a platform behavior, not a RATIO rule. RATIO documents it here so that the user can design intentional overrides across layers and predict their outcome.

### Assigning instructions to layers

```
Does the instruction change between messages?
├── Yes → L₅
└── No
    ├── Does it change between projects?
    │   ├── Yes → L₂
    │   └── No
    │       ├── Is it a procedure contingent on a specific task type?
    │       │   ├── Yes → L₄
    │       │   └── No
    │       │       ├── Does it govern the linguistic form of the output?
    │       │       │   ├── Yes → L₃
    │       │       │   └── No → L₁
```

## Facet architecture

### Facet table

Arbitrary: the facet taxonomy is a user design decision and admits reorganization.

| Facet | Description | Layer | Obligation |
|-------|-------------|-------|------------|
| F₁ or interlocutor profiles | Declarations about the user's profile and the model's stance toward the user | L₁ | Required |
| F₂ or epistemic policies | Policies governing how the model manages evidence, uncertainty, and user errors | L₁ | Required |
| F₃ or global constraints | Persistent constraints on output that apply across all conversations and projects | L₁ | Optional |
| F₄ or domain profile | Declarations about the project's disciplinary domain, its conventions, and the model's role within it | L₂ | Required |
| F₅ or project objective | Instructions defining the global outcome the project must produce | L₂ | Required |
| F₆ or source policies | Policies governing the management of knowledge sources available to the project | L₂ | Required |
| F₇ or project constraints | Persistent constraints on output within the project that supplement F₃ | L₂ | Optional |
| F₈ or lexical-semantic instructions | Instructions governing word selection and usage | L₃ | Required |
| F₉ or morphosyntactic instructions | Instructions governing sentence construction | L₃ | Required |
| F₁₀ or pragmatic-tonal instructions | Instructions governing the model's discursive attitude | L₃ | Required |
| F₁₁ or discourse instructions | Instructions governing cohesion and coherence at the suprasentential level | L₃ | Required |
| F₁₂ or style examples | Output samples that simultaneously instantiate F₈–F₁₁ | L₃ | Required |
| FS₁ or SKILL.md | Main document defining the skill's procedure, dependencies, and constraints | L₄ | Required |
| FS₂ or scripts | Executable scripts that automate operations defined in FS₁ | L₄ | Optional |
| FS₃ or references | Reference materials the skill needs for execution | L₄ | Optional |
| FS₄ or assets | Static files the skill consumes or produces | L₄ | Optional |
| F₁₃ or task specification | Instructions defining the specific operation the model executes in response to the message | L₅ | Required |
| F₁₄ or task scope | Instructions delimiting the topical scope of the task | L₅ | Optional |
| F₁₅ or task constraints | Ephemeral constraints that apply exclusively to the current message and supplement F₃ and F₇ | L₅ | Optional |
| F₁₆ or task examples | Output samples for the task | L₅ | Optional |

L₄ facets use the FS prefix (skill facet) because their physical substrate is files in a directory, not text in a configuration field. This substrate difference determines structural differences: FS₁ is a markdown document, FS₂ are executable scripts, FS₃ and FS₄ are data files. Facets in all other layers are plain text injected into the inference context. L₄ facets are assigned by file type rather than by decision tree, because each facet corresponds to a distinct file category within the skill directory. The internal structure of each file is governed by the platform's skill specification, not by RATIO.

### Assigning instructions to facets

**L₁ or user preferences**

```
Does the instruction define who the user is or how the model positions itself toward the user?
├── Yes → F₁
└── No
    ├── Does it govern how the model treats evidence, uncertainty, or error?
    │   ├── Yes → F₂
    │   └── No → F₃
```

**L₂ or project instructions**

```
Does the instruction govern the management of the project's knowledge sources?
├── Yes → F₆
└── No
    ├── Does it define the expected global outcome of the project?
    │   ├── Yes → F₅
    │   └── No
    │       ├── Does it define the domain, its conventions, or the model's role?
    │       │   ├── Yes → F₄
    │       │   └── No → F₇
```

**L₃ or style**

```
Is the instruction a concrete output sample?
├── Yes → F₁₂
└── No
    ├── Does it govern which words are used or avoided?
    │   ├── Yes → F₈
    │   └── No
    │       ├── Does it govern how sentences are constructed?
    │       │   ├── Yes → F₉
    │       │   └── No
    │       │       ├── Does it govern discursive attitude (formality, tone, politeness)?
    │       │       │   ├── Yes → F₁₀
    │       │       │   └── No → F₁₁
```

**L₅ or prompt**

```
Is the instruction an output example for this task?
├── Yes → F₁₆
└── No
    ├── Does it define what operation the model executes?
    │   ├── Yes → F₁₃
    │   └── No
    │       ├── Does it delimit the topical scope of the task?
    │       │   ├── Yes → F₁₄
    │       │   └── No → F₁₅
```

## Instantiation checklist

Use this checklist when creating or auditing an instantiation. An instantiation is valid when every item returns yes.

**Assignment.**
1. Does every instruction resolve to exactly one layer through the layer assignment tree?
2. Does every instruction resolve to exactly one facet through the corresponding facet assignment tree, or, for L₄, through file type?
3. Does every required facet in the active layers contain at least one instruction?

**Drafting.**
4. Does every sentence contain a single instruction? (Atomicity)
5. Does every constraint use the form "Do Y instead of X," with negative form reserved for irreducible exclusions? (Positive framing)
6. Would a reader without prior context interpret each instruction in exactly one way? (Operational specificity)
7. Does each instruction appear in exactly one facet with no information duplicated from another instruction? (Non-redundancy)
8. Is the format of the instructions consistent with the desired output format? (Format-content coherence)
9. Is emphatic language absent except where the model demonstrably tends to ignore the instruction? (Calibrated intensity)

**Integrity.**
10. Where instructions across different layers appear to conflict, is each conflict an intentional override whose resolution under the platform's precedence order produces the desired behavior?
