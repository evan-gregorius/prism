# PRISM Framework Definition

**Status:** Early-stage research-software framework  
**Current implementation:** Application 001 — Arterial Stenosis  
**Last updated:** 20 July 2026

## Purpose

PRISM is a proposed framework for building fast forward and inverse engineering models by combining:

1. an inexpensive first-principles baseline model, and
2. a reduced, physics-informed correction learned from the difference between that baseline and higher-fidelity data.

In simple terms:

> **predicted high-fidelity result = fast baseline result + reduced learned correction**

The goal is not to replace physics with a neural network. The baseline should capture the main behavior of the system, while the learned component focuses only on the part the baseline does not represent well.

## Core modeling pattern

A PRISM application begins with a low-cost model such as an analytical model, a zero-dimensional or one-dimensional model, or a coarse numerical model.

The baseline and a higher-fidelity reference are evaluated for the same case. Their difference is treated as a discrepancy field. Proper orthogonal decomposition or another reduced-basis method may then compress that discrepancy into a small set of modes.

A trained model predicts the reduced correction coefficients and, when needed, unknown physical or geometric parameters. Reduced governing equations may be added as physics-based constraints after the basic data, mapping, and reconstruction pipeline has been verified.

PRISM is intended to support two related tasks:

- **Forward modeling:** known inputs and parameters are used to predict corrected fields and derived outputs.
- **Inverse modeling:** limited observations are used to estimate unknown parameters and reconstruct the corrected fields that are consistent with them.

## Reusable framework responsibilities

The reusable PRISM core may eventually provide:

- Interfaces for low-cost baseline models
- Mapping between baseline and higher-fidelity field representations
- Construction and storage of discrepancy fields
- POD and related reduced-basis tools
- Reduced-operator and Galerkin-projection utilities
- Forward and inverse inference interfaces
- Handling of incomplete observations
- Uncertainty, correction-magnitude, and out-of-domain checks
- Validation, benchmarking, and reporting utilities
- Interfaces for later scientific visualization

These components should remain independent of any one engineering field wherever practical.

## Application responsibilities

Each application remains responsible for its own:

- Geometry and parameter definitions
- Domain-specific governing assumptions
- Material or fluid models
- Data sources and simulation setup
- Measurement definitions
- Derived quantities and post-processing
- Validation criteria
- Intended-use limits and safety boundaries

Application-specific assumptions should not be placed in the reusable core merely because they are needed by the first implementation.

## Application-first development

PRISM will be developed from working applications rather than from speculative abstractions.

The first implementation will proceed in stages:

1. Establish a controlled benchmark and compare candidate baseline models.
2. Define a consistent geometry and field representation.
3. Generate or obtain a small verified higher-fidelity dataset.
4. Compare full-field reduction with discrepancy reduction.
5. Keep the discrepancy approach only if it provides measurable benefit.
6. Implement a simple forward correction model.
7. Add reduced physics after the data and reconstruction path is verified.
8. Formulate a controlled inverse problem.
9. Add noise, missing observations, uncertainty, and reliability checks in stages.
10. Build a scientific interface only after the underlying outputs are trustworthy.
11. Extract reusable framework components when repeated interfaces become clear.
12. Consider another application only after the first workflow is stable.

The repository structure may anticipate reuse, but the code should not be generalized before the need for an interface has been demonstrated.

## Application 001: arterial stenosis

The first application investigates inverse reduced-order modeling of arterial stenosis.

A fast hemodynamic baseline will be corrected using reduced discrepancy fields derived from higher-fidelity velocity and pressure data. The first inverse test will estimate stenosis severity from controlled sparse observations while other inputs remain known.

The application will own its stenosis geometry, blood-flow assumptions, CFD benchmark, observation model, hemodynamic outputs, validation criteria, and non-clinical use limits.

Success in this application would support the framework design, but it would not by itself prove that PRISM works across unrelated engineering domains.

## Intended contribution

PRISM is an original software architecture and research implementation that connects established public methods into a coherent and testable workflow.

The project may contribute through:

- Research synthesis
- Independent software design and implementation
- Reproducible application workflows
- Comparison with simpler methods
- Clear reporting of failure cases and design limits
- Uncertainty and reliability communication
- Accessible scientific documentation and visualization

PRISM does not claim to have invented proper orthogonal decomposition, Galerkin projection, physics-informed neural networks, reduced-order modeling, discrepancy correction, inverse modeling, or other established methods on which it builds.

## Validation standard

No method will be accepted merely because the code runs or a training loss decreases.

Each stage should be checked against suitable reference data using documented measures of field error, parameter error, conservation, reconstruction quality, computational cost, uncertainty, and behavior outside the supported parameter range.

Simpler models will remain comparison cases. A negative result is acceptable when it clearly shows that a proposed correction, reduction, physical constraint, or inverse formulation does not provide enough benefit.

## Current status

PRISM is currently in the documentation, architecture, literature-review, and feasibility-planning stage.

No general performance, cross-domain validity, or superiority has been demonstrated. The first baseline, benchmark reproduction, snapshot library, reduced correction model, and inverse model remain to be implemented and validated.