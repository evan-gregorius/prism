# PRISM Framework Separation, Public Repository Reorganization, and Scope Refinement — 2026-07-16

## Entry purpose

This entry records the next major project-level change after the July 11 architecture pivot.

The July 10 entry defined a single arterial-stenosis research project centered on a geometry-aware POD-Galerkin PINN. The July 11 entry then changed the technical architecture from a direct full-field surrogate to a first-principles baseline plus a reduced learned discrepancy correction.

The current change separates that general modeling pattern from the first biomedical use case.

The project is now organized as:

**PRISM reusable framework + application-specific implementations**

The arterial-stenosis project remains the first and currently only planned implementation. It is not being abandoned or weakened. Instead, its reusable mathematical and software components are being separated from its hemodynamic assumptions, datasets, geometry, validation measures, and medical-use limitations.

The working public description is:

> PRISM is a proposed framework for constructing fast forward and inverse engineering models by combining an inexpensive first-principles baseline with a reduced, physics-informed correction learned from higher-fidelity discrepancies.

This remains a proposed research-software framework. No general performance, cross-domain validity, or superiority has yet been demonstrated.

## Progress since the previous notebook entry

Several immediate actions from the July 11 entry have been completed or partially completed.

### Completed or substantially advanced

- Research Project Definition Document version 0.2 was created to record the baseline-plus-discrepancy architecture.
- Version 0.1 was preserved as the record of the earlier full-field POD-Galerkin PINN formulation.
- The roles of the baseline, discrepancy, correction, reconstructed fields, inverse parameters, and derived outputs were clarified.
- Candidate first-principles baselines were identified, including analytical, zero-dimensional, one-dimensional, and coarse-field approaches.
- The virtual-cohort concept was documented with variable categories, physiological constraints, missing-data handling, validation concerns, and privacy boundaries.
- The broader literature search was expanded to include POD-Galerkin PINNs, geometry-informed blood-flow ROMs, inverse cardiovascular PINNs, virtual arterial populations, stenosis CFD benchmarks, and uncertainty-aware computational modeling.
- A consolidated `references.md` file was prepared for the stenosis application.
- The intended contribution was narrowed from a possible broad invention claim to an independently implemented and evaluated synthesis of established public research.
- A public PRISM repository and monorepo structure were selected as the new organizational direction.

### Not yet completed

- No baseline model has been selected or implemented.
- The Zhou-type stenosis benchmark has not yet been reproduced.
- No CFD snapshot library has been generated or audited.
- No full-field or discrepancy POD comparison has been run.
- No Galerkin operators, PINN, forward correction model, or inverse model have been implemented.
- No uncertainty method has been selected.
- No frontend has been started.
- Repository licensing has not yet been finalized.
- The proposed engine has not yet been shown to generalize beyond the stenosis application.

The project therefore remains in documentation, architecture, literature review, and feasibility-planning stages.

## Decision 006 — Establish PRISM as the umbrella framework

The reusable modeling pattern will be developed under the name **PRISM**.

PRISM will contain components that may be reusable across different engineering domains:

- Interfaces for inexpensive first-principles baseline models
- Geometry or coordinate mapping between model fidelities
- Construction and storage of high-fidelity discrepancy fields
- POD and related reduced-basis methods
- Reduced operator and Galerkin-projection utilities
- Physics-informed coefficient and parameter inference
- Forward and inverse workflow interfaces
- Missing-observation handling
- Uncertainty and reliability outputs
- Out-of-domain and correction-magnitude checks
- Validation, benchmarking, and reporting utilities
- Interfaces that later support interactive visualization

PRISM should not contain arterial assumptions in its reusable core unless they are expressed through a general interface.

Examples of application-specific material that should not be treated as core engine logic include stenosis geometry equations, blood-rheology choices, Doppler or cuff-pressure observation definitions, wall-shear-stress post-processing, and medical-use disclaimers.

## Decision 007 — Treat arterial stenosis as Application 001

The existing stenosis work will become the first application of PRISM rather than the name and boundary of the entire software project.

The stenosis application will own:

- Parameterized stenosis geometry
- Hemodynamic baseline-model choices
- CFD benchmark reproduction
- Blood density and rheology assumptions
- Velocity and pressure field definitions
- Stenosis-specific measurement models
- Severity, lesion length, and lesion-location inference
- Pressure-drop, wall-shear-stress, and recirculation post-processing
- Virtual arterial cohort construction
- Biomedical validation criteria
- Research-only and non-clinical intended-use boundaries

The application may use PRISM interfaces, but the reusable engine should not be described as proven merely because the stenosis implementation works.

A second application should be introduced only after the first implementation reveals which components are actually reusable. Reuse should be demonstrated rather than assumed from the folder structure.

## Proposed repository boundary

The project is being reorganized as one monorepo:

```text
prism/
├── README.md
├── src/
│   └── prism/
├── applications/
│   └── stenosis/
├── docs/
│   ├── framework/
│   ├── decisions/
│   └── governance/
├── examples/
├── tests/
└── data/
```

The intended ownership of each major area is:

- `src/prism/`: reusable framework code
- `applications/stenosis/`: stenosis-specific code, configuration, studies, and documentation
- `docs/framework/`: PRISM-level definition, architecture, contribution hypothesis, validation philosophy, and roadmap
- `docs/decisions/`: cross-project architecture decision records
- `docs/governance/`: intended-use, independence, attribution, licensing, and agent-assistance records
- `examples/`: small runnable demonstrations of general PRISM components
- `data/`: data instructions and manifests rather than an uncontrolled collection of research files

The earlier stenosis repository should be preserved as a historical record, then archived after the material has been verified in the PRISM monorepo.

## Decision 008 — Reframe the contribution as translational research software

PRISM will not presently be described as a newly invented branch of reduced-order modeling.

The project will instead be framed as an original, independently implemented research-software framework that connects established methods that are often presented separately across papers and prototypes.

The intended project identity is:

> Take advanced public research that exists in disconnected papers and turn it into a coherent, validated, reusable, and unusually accessible engineering system.

The contribution may include:

- Research synthesis
- Independent software architecture and implementation
- Reproducible application workflows
- Careful comparison with simpler models
- Explicit failure and design-envelope reporting
- Uncertainty and reliability communication
- Interactive scientific visualization
- Documentation that allows the method to be inspected and reused

The project does not claim invention of:

- Proper orthogonal decomposition
- Galerkin projection
- Physics-informed neural networks
- Reduced-order modeling
- Discrepancy correction
- Inverse hemodynamics
- Virtual patient populations

Writing the implementation from scratch establishes authorship of the code, documentation, interfaces, and design decisions. It does not create ownership of the underlying public mathematical methods.

## Decision 009 — Make the repository public while preserving claim boundaries

The previous decision to keep the work private until a validated prototype is superseded.

The project will be developed publicly because:

- The work is intentionally based on public research and independently written code.
- The primary near-term purpose is documentation, learning, portfolio development, reproducibility, and research collaboration.
- Public development provides a visible record of the project’s evolution.
- The work is deliberately separated from Department of Defense tasks, code, data, methods, equipment, and nonpublic information.

Public availability does not mean that the software is already validated, clinically usable, generally applicable, or open for unrestricted reuse.

The repository should clearly state:

- Early-stage research status
- Lack of validated performance
- No safety-critical or clinical intended use
- No government, university, or employer endorsement
- Independent development using personal resources and public or authorized information
- Agent-assisted workflow disclosure
- Source, dataset, dependency, and license requirements

A public repository without a software license remains publicly viewable but does not provide a general reuse grant. License selection remains a separate deliberate decision.

## Decision 010 — Keep the first implementation application-first and stage-gated

Separating a reusable engine from an application creates a risk of designing abstractions before the real modeling workflow is understood.

To avoid this, PRISM will be extracted from the stenosis implementation gradually.

The implementation sequence will remain:

1. Establish the stenosis benchmark and candidate baselines.
2. Define a consistent field and geometry representation.
3. Generate a small verified high-fidelity dataset.
4. Compare full-field POD with discrepancy POD.
5. Retain the discrepancy approach only if it provides measurable benefit.
6. Implement a simple forward correction model.
7. Add reduced physics only after the data path and reconstruction are verified.
8. Formulate an inverse severity problem with controlled synthetic observations.
9. Add noise, missing observations, and uncertainty in stages.
10. Build the scientific interface only after the underlying outputs are trustworthy.
11. Extract reusable PRISM components when repeated interfaces become clear.
12. Consider a second application only after the first workflow is stable.

The folder structure may anticipate reuse, but the code should not be generalized prematurely.

## Decision 011 — Treat agent-assisted development as workflow acceleration

The project will use agent-assisted development for tasks such as:

- Research discovery and comparison
- Drafting and reorganizing documentation
- Code scaffolding
- Debugging support
- Test generation
- Refactoring suggestions
- Interface prototyping
- Repetitive implementation work

The human role remains responsible for:

- Selecting the technical direction
- Defining system architecture
- Deciding which sources and methods are relevant
- Reviewing generated code
- Checking equations, assumptions, and units
- Designing validation tests
- Interpreting results
- Rejecting unsupported claims
- Approving public releases

The project should not defensively minimize agent assistance or inaccurately imply that every line was manually typed without assistance. It should state plainly that agentic workflows accelerate development while technical accountability, review, and validation remain human responsibilities.

Consequential assistance should be traceable through engineering-notebook entries, commit history, source records, tests, and review notes. Recording every prompt is not currently required.

## Revised scope

### In scope for the current phase

- PRISM framework definition and architecture
- Public monorepo organization
- Migration of the stenosis documents
- Source and license tracking
- Intended-use, independence, contribution, and agent-assistance statements
- Literature and dataset matrix
- Reproduction of a controlled stenosis benchmark
- Comparison of candidate first-principles baselines
- Pilot discrepancy construction
- Full-field versus discrepancy POD comparison
- Simple forward correction experiment
- Simple inverse stenosis-severity experiment
- Initial reliability and out-of-domain measures
- A limited research interface after validation

### Deferred

- Arbitrary patient-specific anatomy
- Clinical diagnosis or treatment recommendations
- Claims of patient-level accuracy
- Large patient-derived generative models
- Multiple unknown lesion parameters in the first inverse test
- Full three-dimensional pulsatile FSI
- A universal cross-domain engine
- A second engineering application
- Commercial hosting or enterprise deployment
- Proprietary customer modules
- Formal patent claims
- A polished production frontend before technical validation

### Explicitly excluded from the current project

- Government code, data, facilities, equipment, or nonpublic information
- Direct transfer of Department of Defense methods or project content
- Safety-critical operational use
- Clinical decision-making
- Claims that established public methods were invented by PRISM
- Redistribution of datasets, papers, or code without confirmed permission

## Commercial and professional direction

The current project remains an open research and portfolio effort rather than an active commercial product.

Possible future value may come from:

- Custom reduced-order modeling
- Baseline-model correction using customer simulation data
- Scientific workflow integration
- Simulation automation
- Validation and uncertainty studies
- Interactive engineering visualization
- Training, support, and technical consulting
- Private application modules or deployment work

These possibilities do not change the first technical objective: produce one credible, validated, reproducible application.

Any later compensated work should receive a separate review for employment, conflict-of-interest, ownership, licensing, data-use, and intended-use requirements.

## Updated tentative documentation plan

The next stable documents should be separated by level.

### PRISM-level documents

- `docs/framework/prism_framework_definition.md`
- `docs/framework/architecture.md`
- `docs/framework/contribution_hypothesis.md`
- `docs/framework/validation_framework.md`
- `docs/framework/roadmap.md`
- `docs/governance/intended_use.md`
- `docs/governance/independence_statement.md`
- `docs/governance/ai_assistance.md`
- `docs/governance/contributions_and_attribution.md`
- A source, dependency, dataset, and license register

### Stenosis-application documents

- Updated Research Project Definition Document
- Application README
- Engineering notebook
- Stenosis reference list
- Dataset and variable matrix
- Benchmark specification
- Validation plan
- Experiment records

The next Research Project Definition Document should remain focused on inverse stenosis modeling. It should explain that the project is implemented as Application 001 of PRISM, but it should not become a second full definition of the general engine.

## Immediate next actions

1. Save this notebook entry and commit it as the record of the engine/application separation.
2. Create the minimum PRISM monorepo structure.
3. Migrate the stenosis documents into `applications/stenosis/`.
4. Preserve version 0.1 and version 0.2 in the application archive/current structure.
5. Add the consolidated stenosis `references.md`.
6. Draft the PRISM framework definition.
7. Draft the root README and the stenosis application README.
8. Create intended-use, independence, attribution, and agent-assistance records.
9. Create a source and license register before copying external code or data.
10. Produce the next stenosis RPDD version reflecting the PRISM relationship and current public-repository posture.
11. Return to the first technical gate: benchmark and baseline selection.
12. Do not begin the polished frontend until the first validated computational outputs exist.

## Decision log

### Decision 006 — Establish PRISM as the umbrella framework

Reusable baseline, discrepancy, reduction, inference, uncertainty, validation, and visualization interfaces will be developed under PRISM.

### Decision 007 — Treat stenosis as Application 001

Hemodynamic assumptions, datasets, outputs, validation measures, and medical-use limits remain inside the stenosis application.

### Decision 008 — Frame the contribution as an original implementation and synthesis

The project will emphasize coherent implementation, validation, accessibility, and documented application rather than broad invention claims.

### Decision 009 — Develop the project in a public monorepo

The previous private-repository decision is superseded, subject to strict separation from government work and clear early-stage disclaimers.

### Decision 010 — Generalize only after application evidence

The stenosis workflow will drive the first implementation. Reusable components will be extracted only when the interfaces are demonstrated in practice.

### Decision 011 — Use agent-assisted development with human technical accountability

Agentic workflows may accelerate implementation, but system architecture, technical review, validation, interpretation, and public claims remain human responsibilities.
