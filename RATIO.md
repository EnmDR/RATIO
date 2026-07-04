# RATIO

*Specification of an instructional configuration schema for Claude in claude.ai*

| | |
|---|---|
| **Author** | Enmanuel Damas Reyes |
| **Version** | 3.0 |
| **Date** | 2026-07-04 |
| **Status** | Draft |
| **Platform** | claude.ai (Anthropic) |
| **Supersedes** | v2.0 (2026-05-07) |

## Changes from v2.0

Version 3.0 incorporates two platform changes and two empirical updates that postdate v2.0.

1. **Style channel retired (platform).** Anthropic is migrating styles to skills (Claude Help Center, "Styles are moving to skills", 2026). Custom styles become skills, disabled by default, invoked by the slash command `/{style-name}-style`; the Concise, Explanatory and Formal presets are removed and the styles menu is being withdrawn. RATIO no longer treats style as a layer. The five-channel model becomes four user-authored channels.

2. **Layer renumbering.** With style removed, layers are renumbered: v2.0 L₄ (skills) → v3.0 L₃; v2.0 L₅ (prompt) → v3.0 L₄. The v2.0 L₃ (style) is removed. Facets are renumbered accordingly (see facet table). The retired linguistic-form facets (v2.0 F₈–F₁₂) survive only as an optional organizing vocabulary, no longer bound to a layer.

3. **Instruction-following capacity recalibrated (empirical).** The v2.0 load ceiling of "approximately 20 simultaneous instructions" was justified as a model-capacity limit. That justification is empirically obsolete. The constraint is reframed below as a verification-and-interaction limit, not a capacity limit.

4. **Activation scope introduced as a primary assignment criterion (conceptual).** Because skills activate conditionally (semantic match or explicit invocation), an instruction placed in a skill lapses whenever the skill does not fire. The decisive question for any persistent form or constraint instruction is therefore always-active versus task-activated, and this question now governs layer assignment.

## Scope

RATIO is an instructional design framework that organizes user-configurable instructions in claude.ai. It assigns each instruction to one of four layers—corresponding to the platform's four user-authored channels (user preferences, project instructions, skills, prompt)—and, within each layer, to a thematic facet that determines the type of content it admits.

RATIO is a schema, not content. It defines the structure, assignment rules, and drafting principles for instructions, but contains no instructions itself. A concrete set of instructions assigned to facets for a specific user, project, or task is an **instantiation** of RATIO. The framework is stable across instantiations; the content of each facet varies.

RATIO governs two things: where instructions are assigned (layers and facets) and how instructions are drafted (drafting principles). The drafting principles apply to instructions in all layers, including those within skill files. RATIO does not govern platform behavior (how the model resolves conflicts, how skills are triggered, how context is managed), the structural conventions of platform artifacts (the format of skill frontmatter, file organization within skill directories), or the content of any particular instantiation.

**Derived memory is out of scope.** Anthropic deployed persistent memory to all users in early March 2026. Memory injects content into every inference and can interact with—or contradict—authored instructions, but it is derived from past conversations rather than authored by the user as instruction. RATIO governs only user-authored instructions and therefore excludes memory. The interaction is noted where relevant: where an instantiation depends on behavior that memory could override, verify the behavior with memory in its intended state (enabled or disabled).

## Motivation

Without an explicit architecture, instructions tend to be duplicated across channels, assigned to the wrong channel, or contradicted between layers. Duplication consumes context window without contributing new signal. Misassignment prevents an instruction from activating when it should, or keeps it active when it should not. The most consequential misassignment is placing a persistent instruction in a conditionally activated channel: a formatting rule lodged in a semantically activated skill silently lapses on every turn that does not trigger the skill. Accidental contradiction introduces ambiguity that the model resolves non-deterministically.

RATIO prevents these conditions through two mechanisms: a decision procedure that assigns each instruction to exactly one layer and exactly one facet, and a set of drafting principles that govern the linguistic form of instructions. This makes it possible to distinguish between accidental contradiction within a layer (a defect) and intentional overrides across layers (a design pattern whose predictability depends on the type of conflict involved, as described in the precedence section).

## Definitions

**Instruction.** The minimal unit of information that governs model behavior. Each instruction is assigned to exactly one facet. Each instruction takes one of three linguistic forms:
- Declarative: "X is Y."
- Policy: "Do X", "When X, do Y" or "Do X in manner Y."
- Constraint: preferred form "Do Y instead of X." Negative form ("Do not do X") reserved for exclusions that cannot be reformulated positively.

An instruction may include a **traceability annotation** indicating related facets whose content informed its formulation. Traceability annotations are parenthetical references at the end of the instruction (e.g., "cf. F₁", "cf. F₄"). The instruction remains assigned to exactly one facet; the annotation documents dependencies without duplicating content. Traceability is the mechanism by which a skill points to always-active conventions held in L₁ rather than restating them.

**Layer.** A platform channel to which instructions are assigned. Each layer has a persistence (which conversations the instructions are effective in) and an activation (under what condition they enter the inference context).

**Activation scope.** The condition under which a layer's instructions enter the inference context. Two values matter for assignment: *always-active* (L₁ in every conversation; L₂ in every conversation of its project) and *conditionally active* (L₃ skills, by semantic match or explicit slash-command invocation; L₄ prompt, only in the message that carries it). An instruction whose effect must be guaranteed on every turn must reside in an always-active layer.

**Facet.** A thematic category of instructions within a layer. Each facet has an obligation status whose value depends on the instantiation (see obligation assessment below).

**Form vocabulary (optional).** A controlled vocabulary for classifying instructions that govern the linguistic and presentational form of output: lexical-semantic (word selection), morphosyntactic (sentence construction), pragmatic-tonal (discursive attitude), discourse (suprasentential cohesion), and exemplary (output samples). In v2.0 these were a layer (the style channel). In v3.0 they are not a layer; they are an optional scheme for organizing form instructions wherever those instructions are assigned—within F₃ when the form rule is always-active, or within a skill when it is bound to a deliverable.

**Instantiation.** A concrete set of instructions populating the facets of one or more layers for a specific user, project, or task. An instantiation is valid if every instruction satisfies the assignment procedure and drafting principles defined below.

**Instructional load.** The total number of instructions that converge in a single inference. This count includes all active instructions from L₁, L₂ (if in a project), any activated L₃ skill, and L₄. The instructional load is relevant to verification and interaction management, not—at current model capability—to a hard ceiling on what the model can track (see load assessment below).

## Drafting principles

The linguistic form of instructions affects how the model interprets them. These principles govern instruction drafting across all layers.

**Atomicity.** Each sentence contains a single instruction. Compound instructions are decomposed into independent sentences.

**Positive framing.** Instructions describe desired behavior rather than prohibited behavior. The form "Do Y instead of X" is preferred over "Do not do X." The negative form is reserved for exclusions that cannot be reformulated positively. This principle has both first-party and empirical support. Anthropic's own model guidance states that positive examples of the desired behavior tend to be more effective than negative instructions that tell the model what not to do (Claude Platform migration guidance, 2026). Independently, DIM-Bench (Hwang et al., 2025) demonstrates that LLMs are disproportionately vulnerable to negative and distractor requirements, complying with them less reliably than with equivalent positive requirements.

**Operational specificity.** Each instruction passes the unambiguous interpretation test: a reader without prior context would interpret it in exactly one way. Instructions tell the model what to do with information, not merely what information exists. A pointer that names a location without issuing an action ("the conventions are in file X") is not an instruction; the operative form is "load and apply file X before producing output." This matters more with recent models: Anthropic notes that Claude Opus 4.7 and later interpret instructions more literally and do not infer requests that were not made (Claude Platform migration guidance, 2026), so a file that is named but not commanded to load may simply not load.

**Positional precedence.** Within a facet, the highest-priority instructions are placed first. Order effects are documented in the instruction-following literature: items presented earlier receive more attention (Zeng et al., 2025; Liu et al., 2025; Wen et al., 2024). Where an instantiation has been prioritized for load (see below), the surviving high-priority instructions are also positioned early, so that ordering reinforces rather than contradicts the prioritization.

**Non-redundancy.** Each instruction appears in exactly one facet. Information already implied by an existing instruction is not repeated. Where an instruction has dependencies on content in another facet—most commonly a skill that relies on always-active conventions in L₁—a traceability annotation is used instead of duplication.

**Format-content coherence.** The format in which instructions are written models the desired output format. If the desired output is prose, instructions are written in prose.

**Calibrated intensity.** With frontier models, standard declarative instructions suffice. Emphatic language (capitalization, obligation adverbs) is reserved for instructions the model demonstrably tends to ignore, not used as a default. Determining which instructions require emphasis is an empirical question that must be resolved per instantiation through testing.

**Instructional parsimony.** The number of instructions in a configuration should be the minimum that produces the desired behavior. The rationale is no longer that the model loses track of large instruction sets—at current capability it largely does not (see load assessment)—but that every instruction is something a human must maintain and verify, and that instructions which merely restate the model's reliable default behavior consume maintenance budget and verification surface without benefit. Before adding an instruction, verify that the model's default behavior in the relevant dimension is inadequate.

## Layer architecture

### Layer table

Grounded: the layer architecture is determined by the platform and is not user-modifiable.

| Layer | Description | Persistence | Activation |
|-------|-------------|-------------|------------|
| L₁ or user preferences | Instructions that govern the model's baseline behavior across all interactions | All conversations | Always active |
| L₂ or project instructions | Instructions that govern the configuration of a specific project | Project conversations | Always active within the project |
| L₃ or skills | Instructions that govern procedures and deliverable-specific form contingent on a task type | All conversations | Conditionally active (semantic match, or explicit slash-command invocation for migrated styles) |
| L₄ or prompt | Instructions that govern the execution of a specific task in a single message | Single message | One inference |

Two platform behaviors of L₃ bear on assignment and reliability. First, a skill activates only when its description semantically matches the task, or—for skills migrated from styles—when the user types its slash command; instructions inside a skill are inert on every turn that does not trigger it. Second, within an activated skill, `references/` files load by progressive disclosure: they enter context only when `SKILL.md` directs the model to load them or the model judges them necessary. A convention that must apply to every output of a skill therefore belongs in the `SKILL.md` body, or in a reference file that `SKILL.md` loads with an explicit, unconditional directive.

### Precedence

The platform resolves conflicts between layers by generally favoring the instruction with narrower persistence: L₄ overrides L₂, which overrides L₁. L₃ does not cleanly generate precedence conflicts because it activates conditionally and operates on task procedures and deliverable-specific form rather than general behavior; when an active skill's form rule conflicts with an L₁ convention, the more specific and more recently loaded skill content tends to prevail, but this is not guaranteed and should be tested. This resolution order is a platform behavior, not a RATIO rule.

The reliability of this resolution depends on the type of conflict.

**Constraint conflicts** occur when two layers impose quantifiable, mutually exclusive restrictions on the same dimension (e.g., L₂ specifies "maximum 200 words" and L₄ specifies "minimum 500 words"). These are resolved with moderate-to-high reliability in favor of the narrower-persistence layer, because the model can identify the contradiction and apply the precedence rule.

**Orientation conflicts** occur when two layers provide qualitative directives that pull in opposing directions without a clear logical contradiction (e.g., L₁ specifies "be concise" and L₂ specifies "include all relevant mechanisms"). These resolve unpredictably, because the model must balance heuristics rather than apply a binary rule. The output typically reflects a compromise rather than a clean override.

**Cross-dimensional interactions** occur when instructions from different layers operate on different dimensions of the output. These are not conflicts: the model satisfies both. Most interactions between always-active form conventions (in F₃) and the content-governing layers are compositional, because form and content are different dimensions.

Intentional overrides across layers should be designed as constraint conflicts, where resolution is predictable. Overrides that depend on orientation-conflict resolution are unreliable and should be avoided; rewrite the instructions to eliminate the ambiguity instead.

The empirical literature indicates that instruction-hierarchy compliance is imperfect even in frontier models and may vary across model updates. The precedence order here represents the platform's intended behavior, not a guarantee of deterministic resolution. Instantiations that depend critically on cross-layer overrides should include regression tests (see checklist).

### Assigning instructions to layers

```
Does the instruction change between messages?
├── Yes → L₄
└── No
    ├── Is it a procedure or a form rule contingent on a specific task type or deliverable?
    │   ├── Yes
    │   │   └── Must it apply on every turn, including turns that do not trigger the skill?
    │   │       ├── Yes → L₁ (it is not truly task-bound; see activation scope)
    │   │       └── No → L₃
    │   └── No
    │       ├── Does it change between projects?
    │       │   ├── Yes → L₂
    │       │   └── No → L₁
```

The activation-scope gate inside the L₃ branch is the substantive addition over v2.0. A rule that "feels" task-bound (a typographic convention, a citation style) but must in fact govern every response is not task-bound; placing it in a skill guarantees it lapses whenever the skill does not fire. Such rules go to L₁.

## Facet architecture

### Obligation assessment

The obligation status of a facet is not fixed by the schema but assessed per instantiation, depending on whether the model's default behavior in the dimension the facet governs is adequate for the user's needs.

- **Required**: default behavior is inadequate or unpredictable; the facet must contain at least one instruction.
- **Recommended**: default behavior has not been verified as adequate; the facet should contain instructions unless testing confirms the default suffices.
- **Optional**: default behavior is generally acceptable; the facet may contain instructions if the user needs to deviate, but may be left empty.

The table gives default obligation values representing the typical assessment; override them when context warrants.

### Facet table

Arbitrary: the facet taxonomy is a user design decision and admits reorganization.

| Facet | Description | Layer | Default obligation |
|-------|-------------|-------|--------------------|
| F₁ or interlocutor profiles | Declarations about the user's profile and the model's stance toward the user | L₁ | Required |
| F₂ or epistemic policies | Policies governing how the model manages evidence, uncertainty, and user errors | L₁ | Recommended |
| F₃ or global output conventions and constraints | Persistent constraints on output form and behavior that apply across all conversations: presentational and orthotypographic conventions, always-active form rules, and global exclusions | L₁ | Recommended |
| F₄ or domain profile | Declarations about the project's disciplinary domain, its conventions, and the model's role within it | L₂ | Required |
| F₅ or project objective | Instructions defining the global outcome the project must produce | L₂ | Required |
| F₆ or source policies | Policies governing the management of knowledge sources available to the project | L₂ | Recommended |
| F₇ or project constraints | Persistent constraints on output within the project that supplement F₃ | L₂ | Optional |
| FS₁ or SKILL.md | Main document defining the skill's procedure, dependencies, deliverable-specific form, and constraints | L₃ | Required |
| FS₂ or scripts | Executable scripts that automate operations defined in FS₁ | L₃ | Optional |
| FS₃ or references | Reference materials the skill loads on demand for execution, including long deliverable-specific specifications | L₃ | Optional |
| FS₄ or assets | Static files the skill consumes or produces | L₃ | Optional |
| F₈ or task specification | Instructions defining the specific operation the model executes in response to the message | L₄ | Required |
| F₉ or task scope | Instructions delimiting the topical scope of the task | L₄ | Optional |
| F₁₀ or task constraints | Ephemeral constraints applying exclusively to the current message that supplement F₃ and F₇ | L₄ | Optional |
| F₁₁ or task examples | Output samples for the task | L₄ | Optional |

The v2.0 style facets (F₈–F₁₂: lexical-semantic, morphosyntactic, pragmatic-tonal, discourse, examples) are removed as layer-bound facets. Their content is redistributed by activation scope: always-active form rules go to F₃; deliverable-specific form goes to FS₁ or FS₃. The form vocabulary (see Definitions) remains available to organize that content internally.

L₃ facets use the FS prefix because their physical substrate is files in a directory, not text in a configuration field. This substrate difference is structural: FS₁ is a markdown document, FS₂ are executable scripts, FS₃ and FS₄ are data files. L₃ facets are assigned by file type rather than by decision procedure. The internal structure of each file is governed by the platform's skill specification, not by RATIO.

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

**L₄ or prompt**

```
Is the instruction an output example for this task?
├── Yes → F₁₁
└── No
    ├── Does it define what operation the model executes?
    │   ├── Yes → F₈
    │   └── No
    │       ├── Does it delimit the topical scope of the task?
    │       │   ├── Yes → F₉
    │       │   └── No → F₁₀
```

## Instructional load assessment

Before finalizing an instantiation, estimate the instructional load for a typical inference by counting the instructions simultaneously active: all of L₁, plus L₂ if in a project, plus the typical L₄ task specification, plus the average contribution from an activated L₃ skill.

The v2.0 heuristic placed a review threshold at approximately 20 simultaneous instructions and justified it as a capacity ceiling. That justification is empirically obsolete. The IFScale benchmark (Jaroslawicz et al., 2025) found frontier models began dropping instructions at roughly 150–200 simultaneous constraints. Its 2026 replication and extension (Voss, 2026) found that current frontier models hold near-perfect accuracy up to roughly 2 000 named constraints, and in the strongest cases to roughly 5 000. Raw capacity is therefore not the binding constraint for any realistic claude.ai configuration.

Two qualifications keep parsimony relevant despite the capacity headroom.

First, IFScale measures inclusion of discrete named items, a deliberately easy proxy. Its authors state explicitly that the capacity result is evidence that long instruction sets are viable, not proof that every instruction type is followed. Heterogeneous, interacting, and qualitative instructions—especially orientation directives—degrade earlier than the benchmark's keyword-inclusion task suggests, because the failure mode is compromise between competing heuristics rather than forgetting.

Second, the operative cost has shifted from capability to verification and maintenance: Voss (2026) characterizes long skill files as no longer a compression problem but a verification problem. A configuration with many instructions is something a human must keep coherent, test after model updates, and reason about when behavior surprises them.

The revised heuristic, accordingly, is not a capacity ceiling but a review trigger: if a typical inference carries many simultaneous instructions, audit for instructions that restate default behavior (remove them), for orientation conflicts (rewrite them), and for instructions that could move to L₄ so they activate only when relevant. When load cannot be reduced without sacrificing necessary instructions, prioritize: retain instructions whose violation would be most costly, and position them early (cf. positional precedence); stylistic or minor-formatting preferences are the first candidates for removal or demotion.

## Instantiation checklist

Use this checklist when creating or auditing an instantiation. An instantiation is valid when every applicable item returns yes.

**Assignment**

1. Does every instruction resolve to exactly one layer through the layer assignment procedure?
2. Does every instruction resolve to exactly one facet through the corresponding facet procedure, or, for L₃, through file type?
3. For every instruction that "feels" task-bound, has the activation-scope gate been applied—must it apply on turns that do not trigger the skill, and if so, is it in L₁ rather than the skill?
4. Does every facet assessed as Required contain at least one instruction?
5. Has the obligation status of each facet been assessed for this instantiation, or have defaults been adopted with justification?

**Drafting**

6. Does every sentence contain a single instruction? (Atomicity)
7. Does every constraint use the form "Do Y instead of X," with negative form reserved for irreducible exclusions? (Positive framing)
8. Would a reader without prior context interpret each instruction in exactly one way, and does every pointer to another file issue an action rather than merely name a location? (Operational specificity)
9. Are the highest-priority instructions positioned first within their facet? (Positional precedence)
10. Does each instruction appear in exactly one facet, with cross-facet dependencies documented via traceability rather than duplication? (Non-redundancy)
11. Is the format of the instructions consistent with the desired output format? (Format-content coherence)
12. Is emphatic language absent except where the model demonstrably tends to ignore the instruction? (Calibrated intensity)
13. Has each instruction been verified as necessary—the model's default behavior in that dimension is inadequate? (Parsimony)

**Skill reliability**

14. For every convention that must apply to every output of a skill, does it reside in the `SKILL.md` body, or in a reference file loaded by an explicit unconditional directive—rather than in a reference file referenced only by location?

**Load**

15. Has the instructional load for a typical inference been estimated, and audited for default-restating instructions, orientation conflicts, and instructions demotable to L₄?

**Integrity**

16. Where instructions across layers appear to conflict, is each a constraint conflict whose resolution under the precedence order produces the desired behavior?
17. Are there orientation conflicts between layers? If so, have they been resolved by rewriting to eliminate the ambiguity rather than relying on precedence?

**Maintenance**

18. Have 3–5 reference outputs been defined that capture the expected behavior of the instantiation, for use as regression tests after model updates?
19. Is a change log maintained that documents what instructions were modified, when, and why?
20. Where behavior depends on derived memory being enabled or disabled, has it been tested with memory in its intended state?
