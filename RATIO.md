# RATIO

*Specification of an instructional configuration schema for Claude in claude.ai*

| | |
|---|---|
| **Author** | Enmanuel Damas Reyes |
| **Version** | 2.0 |
| **Date** | 2026-04-15 |
| **Status** | Draft |
| **Platform** | claude.ai (Anthropic) |
| **Supersedes** | v1.0 (2026-03-21) |

## Scope

RATIO is an instructional design framework that organizes user-configurable instructions in claude.ai. It assigns each instruction to one of five layers—corresponding to the platform's five channels (user preferences, project instructions, style, skills, prompt)—and, within each layer, to a thematic facet that determines the type of content it admits.

RATIO is a schema, not content. It defines the structure, assignment rules, and drafting principles for instructions, but contains no instructions itself. A concrete set of instructions assigned to facets for a specific user, project, or task is an **instantiation** of RATIO. The framework is stable across instantiations; the content of each facet varies.

RATIO governs two things: where instructions are assigned (layers and facets) and how instructions are drafted (drafting principles). The drafting principles apply to instructions in all layers, including those within skill files. RATIO does not govern platform behavior (how the model resolves conflicts, how skills are triggered, how context is managed), the structural conventions of platform artifacts (the format of skill frontmatter, file organization within skill directories, the format of style presets), or the content of any particular instantiation.

## Motivation

Without an explicit architecture, instructions tend to be duplicated across channels, assigned to the wrong channel, or contradicted between layers. Duplication consumes context window without contributing new signal. Misassignment prevents an instruction from activating when it should, or keeps it active when it should not. Accidental contradiction introduces ambiguity that the model resolves non-deterministically.

RATIO prevents these conditions through two mechanisms: a decision tree that assigns each instruction to exactly one layer and exactly one facet, and a set of drafting principles that govern the linguistic form of instructions. This makes it possible to distinguish between accidental contradiction within a layer (a defect) and intentional overrides across layers (a design pattern whose predictability depends on the type of conflict involved, as described in the precedence section).

## Definitions

**Instruction.** The minimal unit of information that governs model behavior. Each instruction is assigned to exactly one facet. Each instruction takes one of three linguistic forms:
- Declarative: "X is Y."
- Policy: "Do X", "When X, do Y" or "Do X in manner Y."
- Constraint: preferred form "Do Y instead of X." Negative form ("Do not do X") reserved for exclusions that cannot be reformulated positively.

An instruction may include a **traceability annotation** indicating related facets whose content informed its formulation. Traceability annotations are parenthetical references at the end of the instruction (e.g., "cf. F₁", "cf. F₄, F₈"). The instruction remains assigned to exactly one facet; the annotation documents dependencies without duplicating content.

**Layer.** A platform channel to which instructions are assigned. Each layer has a persistence (which conversations the instructions are effective in) and an activation (under what condition they enter the inference context).

**Facet.** A thematic category of instructions within a layer. Each facet has an obligation status whose value depends on the instantiation (see obligation assessment below).

**Instantiation.** A concrete set of instructions populating the facets of one or more layers for a specific user, project, or task. An instantiation is valid if every instruction satisfies the assignment trees and drafting principles defined below.

**Instructional load.** The total number of instructions that converge in a single inference. This count includes all active instructions from L₁, L₂ (if in a project), L₃ (if a style is active), any activated L₄ skill, and L₅. The instructional load determines the practical ceiling on how many instructions the model can follow reliably in a given inference.

## Drafting principles

The linguistic form of instructions affects how the model interprets them. These principles govern instruction drafting across all layers. Each principle is annotated with its evidential basis to allow the user to prioritize compliance effort.

**Atomicity.** Each sentence contains a single instruction. Compound instructions are decomposed into independent sentences. Evidential basis: design heuristic, indirectly supported by the instruction-following literature. Known trade-off: decomposition increases instruction count, which can degrade compliance when instructional load is high (Yang et al., 2025). Apply atomicity to improve clarity, but monitor total instructional load.

**Positive framing.** Instructions describe desired behavior rather than prohibited behavior. The form "Do Y instead of X" is preferred over "Do not do X." The negative form is reserved for exclusions that cannot be reformulated positively. Evidential basis: practitioner convention documented in platform guidelines. No controlled quantitative comparison against negative framing in frontier models has been published as of this version.

**Operational specificity.** Each instruction passes the unambiguous interpretation test: a reader without prior context would interpret it in exactly one way. Instructions tell the model what to do with information, not merely what information exists. Evidential basis: strong empirical support. Underspecified prompts are approximately twice as likely to regress across model or prompt changes, with accuracy drops exceeding 20% (Yang et al., 2025).

**Non-redundancy.** Each instruction appears in exactly one facet. Information already implied by an existing instruction is not repeated as a separate instruction. Where an instruction has dependencies on content in other facets, a traceability annotation is used instead of duplication. Evidential basis: design principle derived from context-window efficiency. Consistent with the promptware engineering principle that prompts should be modular and free of unnecessary repetition (Chen et al., 2025).

**Format-content coherence.** The format in which instructions are written models the desired output format. If the desired output is prose, instructions are written in prose. Evidential basis: practitioner heuristic. Supported by the general finding that LLMs are sensitive to formatting cues in prompts (He et al., 2024).

**Calibrated intensity.** With frontier models, standard declarative instructions suffice. Emphatic language (capitalization, obligation adverbs) is reserved for instructions the model demonstrably tends to ignore, not used as a default. Determining which instructions require emphasis is an empirical question that must be resolved per instantiation through testing. Evidential basis: conceptual support in the promptware literature, which notes that LLMs exhibit human-like interpretive behaviors where rhetorical intensity has non-trivial effects (Chen et al., 2025). Requires empirical calibration per instantiation.

**Instructional parsimony.** The number of instructions active in a single inference should be the minimum necessary to produce the desired behavior. Instructions that merely restate the model's reliable default behavior consume instructional budget without benefit and may degrade compliance with other instructions. Before adding an instruction, verify that the model's default behavior in the relevant dimension is inadequate. Evidential basis: strong empirical support. LLM compliance with individual instructions drops significantly as the total number of simultaneous instructions increases, from approximately 99% with individual instructions to approximately 85% with 19 simultaneous instructions, with some individual requirements suffering drops exceeding 60% due to interference even without semantic conflict (Yang et al., 2025).

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

The platform resolves conflicts between layers by generally favoring the instruction with narrower persistence: L₅ overrides L₂, which overrides L₃, which overrides L₁. L₄ does not generate precedence conflicts because it activates conditionally and operates on task procedures rather than general behavior. This resolution order is a platform behavior, not a RATIO rule.

However, the reliability of this resolution depends on the type of conflict involved. Not all conflicts between layers are resolved with equal predictability.

**Constraint conflicts** occur when two layers impose quantifiable, mutually exclusive restrictions on the same dimension (e.g., L₂ specifies "maximum 200 words" and L₅ specifies "minimum 500 words"). These conflicts are resolved with moderate-to-high reliability in favor of the narrower-persistence layer, because the model can identify the contradiction and apply the precedence rule.

**Orientation conflicts** occur when two layers provide qualitative directives that pull in opposing directions without a clear logical contradiction (e.g., L₁ specifies "be concise" and L₂ specifies "include all relevant mechanisms"). These conflicts are resolved unpredictably, because the model must balance heuristics rather than apply a binary rule. The output will typically reflect a compromise rather than a clean override.

**Cross-dimensional interactions** occur when instructions from different layers operate on different dimensions of the output (e.g., L₃ specifies sentence length while L₂ specifies content depth). These are not conflicts in the strict sense: the model will attempt to satisfy both simultaneously. Most interactions between L₃ and the other layers are compositional rather than conflictive, because L₃ predominantly governs the linguistic form of the output while L₁, L₂, and L₅ predominantly govern its content and behavior.

Intentional overrides across layers should be designed using constraint conflicts, where the resolution is predictable. Designing overrides that depend on orientation conflict resolution is unreliable and should be avoided.

The empirical literature indicates that instruction hierarchy compliance is imperfect even in frontier models and may vary across model updates (Wallace et al., 2024; Zheng et al., 2026). The precedence order described here represents the platform's intended behavior, not a guarantee of deterministic resolution. Instantiations that depend critically on cross-layer overrides should include regression tests (see instantiation checklist) to verify that the intended resolution holds.

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

### Obligation assessment

The obligation status of a facet is not fixed by the schema but assessed per instantiation. The assessment depends on whether the model's default behavior in the dimension governed by the facet is adequate for the user's needs.

- **Required**: The model's default behavior in this dimension is inadequate or unpredictable for the instantiation's purposes. The facet must contain at least one instruction.
- **Recommended**: The model's default behavior in this dimension has not been verified as adequate. The facet should contain instructions unless the user has confirmed through testing that the default suffices.
- **Optional**: The model's default behavior in this dimension is generally acceptable. The facet may contain instructions if the user needs to deviate from the default, but leaving it empty is a valid design choice.

The table below provides default obligation values that represent the typical assessment for most instantiations. These defaults should be overridden when the user's specific context warrants it.

### Facet table

Arbitrary: the facet taxonomy is a user design decision and admits reorganization.

| Facet | Description | Layer | Default obligation |
|-------|-------------|-------|--------------------|
| F₁ or interlocutor profiles | Declarations about the user's profile and the model's stance toward the user | L₁ | Required |
| F₂ or epistemic policies | Policies governing how the model manages evidence, uncertainty, and user errors | L₁ | Recommended |
| F₃ or global constraints | Persistent constraints on output that apply across all conversations and projects | L₁ | Optional |
| F₄ or domain profile | Declarations about the project's disciplinary domain, its conventions, and the model's role within it | L₂ | Required |
| F₅ or project objective | Instructions defining the global outcome the project must produce | L₂ | Required |
| F₆ or source policies | Policies governing the management of knowledge sources available to the project | L₂ | Recommended |
| F₇ or project constraints | Persistent constraints on output within the project that supplement F₃ | L₂ | Optional |
| F₈ or lexical-semantic instructions | Instructions governing word selection and usage | L₃ | Recommended |
| F₉ or morphosyntactic instructions | Instructions governing sentence construction | L₃ | Recommended |
| F₁₀ or pragmatic-tonal instructions | Instructions governing the model's discursive attitude | L₃ | Recommended |
| F₁₁ or discourse instructions | Instructions governing cohesion and coherence at the suprasentential level | L₃ | Recommended |
| F₁₂ or style examples | Output samples that simultaneously instantiate F₈–F₁₁ | L₃ | Recommended |
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

## Instructional load assessment

Before finalizing an instantiation, estimate the instructional load for a typical inference by counting the instructions that will be simultaneously active. The count should include all instructions from L₁, plus L₂ if working within a project, plus L₃ if a style is active, plus the typical L₅ task specification, plus the average contribution from L₄ skills when activated.

There is no universal threshold above which compliance degrades, because degradation depends on the model, the complexity of individual instructions, and the degree of interaction between them. However, the following heuristic provides orientation: if the total instructional load for a typical inference exceeds approximately 20 simultaneous instructions, review the instantiation for instructions that restate the model's default behavior and can be removed without loss.

When the instructional load cannot be reduced further without sacrificing necessary instructions, prioritize. Instructions whose violation would produce the most harmful or costly output should be retained; instructions governing stylistic preferences or minor formatting details should be the first candidates for removal or demotion to the prompt layer (L₅), where they only activate when relevant.

## Instantiation checklist

Use this checklist when creating or auditing an instantiation. An instantiation is valid when every applicable item returns yes.

**Assignment.**

1. Does every instruction resolve to exactly one layer through the layer assignment tree?
2. Does every instruction resolve to exactly one facet through the corresponding facet assignment tree, or, for L₄, through file type?
3. Does every facet assessed as Required in the instantiation contain at least one instruction?
4. Has the obligation status of each facet been assessed for this specific instantiation, or have the default values been adopted with justification?

**Drafting.**

5. Does every sentence contain a single instruction? (Atomicity)
6. Does every constraint use the form "Do Y instead of X," with negative form reserved for irreducible exclusions? (Positive framing)
7. Would a reader without prior context interpret each instruction in exactly one way? (Operational specificity)
8. Does each instruction appear in exactly one facet with no information duplicated from another instruction? (Non-redundancy)
9. Where an instruction depends on content in another facet, is the dependency documented via a traceability annotation rather than duplication? (Non-redundancy, traceability)
10. Is the format of the instructions consistent with the desired output format? (Format-content coherence)
11. Is emphatic language absent except where the model demonstrably tends to ignore the instruction? (Calibrated intensity)
12. Has each instruction been verified as necessary, i.e., the model's default behavior in that dimension is inadequate? (Instructional parsimony)

**Load.**

13. Has the instructional load for a typical inference been estimated?
14. If the load exceeds approximately 20 simultaneous instructions, have low-priority instructions been removed, consolidated, or moved to L₅?

**Integrity.**

15. Where instructions across different layers appear to conflict, is each conflict a constraint conflict (quantifiable, mutually exclusive restrictions on the same dimension) whose resolution under the platform's precedence order produces the desired behavior?
16. Are there any orientation conflicts (qualitative directives pulling in opposing directions) between layers? If so, have they been resolved by rewriting the instructions to eliminate the ambiguity rather than relying on precedence?

**Maintenance.**

17. Have 3–5 reference outputs been defined that capture the expected behavior of the instantiation for use as regression tests after model updates?
18. Is a change log maintained for the instantiation that documents what instructions were modified, when, and why?

## Changelog

### v2.0 (2026-04-15)

Changes from v1.0, motivated by alignment with recent empirical literature on instruction hierarchy (Wallace et al., 2024; Zheng et al., 2026), prompt underspecification (Yang et al., 2025), and promptware engineering (Chen et al., 2025).

1. Added **instructional parsimony** as a drafting principle, addressing the empirically documented trade-off between instruction granularity and compliance degradation under high instructional load.
2. Reformulated the **precedence** section to distinguish between constraint conflicts (predictable resolution), orientation conflicts (unpredictable resolution), and cross-dimensional interactions (compositional, not conflictive). Added a note on the empirical instability of hierarchy compliance.
3. Replaced the binary obligation status (Required/Optional) with a **three-level assessment** (Required/Recommended/Optional) that depends on the instantiation rather than being fixed by the schema. This supports intentional underspecification as a valid design pattern.
4. Added **instructional load assessment** section with heuristics for estimating and managing the total number of simultaneous instructions.
5. Added **traceability annotations** to the definition of Instruction, allowing cross-facet dependencies to be documented without violating non-redundancy.
6. Added **evidential basis annotations** to each drafting principle, distinguishing between empirically supported principles and practitioner heuristics.
7. Added **maintenance items** to the instantiation checklist: regression tests and change log.
8. Expanded the instantiation checklist from 10 to 18 items to cover the new dimensions.

### v1.0 (2026-03-21)

Initial release.

## References

Chen, Z., Wang, C., Sun, W., Liu, X., Zhang, J. M., & Liu, Y. (2025). Promptware engineering: Software engineering for LLM prompt development. arXiv:2503.02400.

He, J., Rungta, M., Koleczek, D., Sekhon, A., Wang, F. X., & Hasan, S. (2024). Does prompt formatting have any impact on LLM performance? arXiv:2411.10541.

Liu, Y.-Y., Zheng, Z., Zhang, F., et al. (2026). A comprehensive taxonomy of prompt engineering techniques for large language models. Frontiers of Computer Science, 20(3), 2003601.

Wallace, E., Xiao, K., Leike, R., Weng, L., Heidecke, J., & Beutel, A. (2024). The instruction hierarchy: Training LLMs to prioritize privileged instructions. NeurIPS 2024.

Yang, C., Shi, Y., Ma, Q., Liu, M. X., Kästner, C., & Wu, T. (2025). What prompts don't say: Understanding and managing underspecification in LLM prompts. arXiv:2505.13360.

Zheng, Z., et al. (2026). Reasoning up the instruction ladder for controllable language models. arXiv:2511.04694.
