# Project Initiation — 2026-07-10

## Project working title

Geometry-Aware POD-Galerkin PINN ROM for Inverse Stenosis Analysis

## Initial research hypothesis

A geometry-aware POD-Galerkin PINN with a constrained learned residual closure can recover stenosis parameters and full hemodynamic fields from sparse, noisy, or incomplete observations more accurately and robustly than purely data-driven reduced-order models.

## Motivation

Computational hemodynamic simulations can provide detailed velocity, pressure, wall shear stress, and recirculation information for stenotic blood flow. However, high-fidelity CFD becomes expensive when evaluating many geometries, operating conditions, inverse problems, or uncertainty cases.

This project will investigate whether proper orthogonal decomposition, Galerkin-projected physics, physics-informed neural networks, and learned residual corrections can be combined into a fast inverse and forward reduced-order modeling framework.

## Initial intended inputs

The first controlled parameter space may include:

- Stenosis severity
- Nondimensional stenosis length
- Reynolds number
- Blood rheology model

Later parameters may include:

- Stenosis location
- Eccentricity
- Vessel curvature
- Pulsatile inlet conditions
- Sparse pressure or velocity observations

## Initial intended outputs

- Estimated stenosis severity
- Estimated stenosis location and geometry
- Reconstructed velocity field
- Reconstructed pressure field
- Wall shear stress
- Pressure drop
- Recirculation-zone properties
- Prediction uncertainty
- Inference time

## Initial technical approach

1. Reproduce an idealized published stenosis CFD benchmark.
2. Generate or otherwise obtain a parameterized CFD snapshot library.
3. Construct POD bases for velocity and pressure.
4. Implement data-driven POD surrogate baselines.
5. Construct Galerkin-projected reduced equations.
6. Train a POD-Galerkin PINN.
7. Formulate inverse estimation from sparse observations.
8. Add a constrained learned residual closure.
9. Test robustness to noise and missing measurements.
10. Build an interactive research interface after validation.

## Current assumptions

- Initial development will use synthetic or anonymized public CFD data.
- Initial geometry will be idealized and axisymmetric.
- No patient data will be used during the first development stage.
- No clinical diagnostic claims will be made.
- The first implementation will prioritize reproducibility and validation over complexity.

## Current uncertainties

- Whether one global POD basis will represent mild and severe stenosis accurately.
- How geometry variation should be mapped into a common reduced space.
- Whether Galerkin-informed learning provides measurable benefit over POD-NN baselines.
- How the learned closure should be constrained.
- Which sparse measurements make stenosis parameters identifiable.
- Whether Carreau rheology should be introduced during the first or second implementation stage.
- What university, publication, intellectual-property, and open-source reviews may be required.

## Initial comparison models

- POD reconstruction
- POD interpolation
- POD-RBF or Gaussian-process surrogate
- POD neural network
- POD-Galerkin ROM
- POD-Galerkin PINN
- POD-Galerkin PINN with residual closure

## Initial validation metrics

- Relative L2 velocity error
- Relative L2 pressure error
- Wall shear stress error
- Pressure-drop error
- Stenosis-severity error
- Stenosis-location error
- Inference time
- Robustness to noise
- Robustness to missing measurements
- Uncertainty calibration
- Performance on unseen in-range parameter combinations

## Immediate next actions

1. Write the formal research charter.
2. Record the security, ownership, publication, and open-source questions.
3. Review the project concept with relevant faculty.
4. Select the first CFD software and benchmark configuration.
5. Establish the initial development environment and repository architecture.

## Decision log

### Decision 001 — Begin with a private repository

The project will remain private until a validated prototype exists and publication, ownership, and open-source considerations have been reviewed.

### Decision 002 — Begin with documentation before solver development

The research hypothesis, scope, assumptions, exclusions, validation metrics, and risks will be documented before implementing the primary CFD and machine-learning code.