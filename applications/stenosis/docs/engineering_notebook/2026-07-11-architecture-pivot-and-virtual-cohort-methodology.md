# Architecture Pivot and Virtual Cohort Methodology — 2026-07-11

## Entry purpose

This entry records a significant refinement to the proposed research architecture and an initial methodology for constructing a large physiologically constrained virtual patient cohort.

The original project direction treated the POD-Galerkin PINN as the primary reduced model responsible for reconstructing the full velocity and pressure fields. A learned closure or residual correction would then be added after the primary model had been established.

The revised direction places a simpler first-principles hemodynamic model at the center of the forward calculation. The POD-Galerkin PINN is instead focused on learning a reduced correction to the errors and unresolved behavior of that baseline model.

The revised architecture is:

First-principles baseline model + reduced learned discrepancy = corrected hemodynamic prediction

This change is intended to prevent the learned model from spending unnecessary capacity reproducing physical behavior that can already be represented inexpensively through conservation laws, analytical relationships, or low-dimensional flow models.

The decisions recorded in this entry remain research hypotheses. The discrepancy field is not yet known to be low dimensional, the appropriate baseline model has not been selected, and the feasibility of inverse stenosis recovery from sparse patient observations has not yet been demonstrated.

## Decision 003 — Refocus the POD-Galerkin PINN on discrepancy closure

### Previous model concept

The original concept followed a more direct interpretation of the POD-Galerkin PINN inverse framework.

The planned sequence was:

1. Obtain full-order velocity and pressure snapshots.
2. Apply POD directly to the complete fields.
3. Construct Galerkin-projected reduced equations.
4. Train a PINN to predict the complete set of reduced coefficients.
5. Infer stenosis parameters from sparse observations.
6. Add a learned closure after the primary reduced model had been established.

Under that approach, the neural network would remain responsible for predicting most of the reduced flow solution.

### Revised model concept

The revised concept introduces an inexpensive first-principles model that captures the dominant or bulk hemodynamic behavior.

Possible baseline models may include:

- An analytical axisymmetric flow model
- A one-dimensional arterial flow model
- A lumped-parameter or resistance model
- A simplified stenosis pressure-loss model
- A low-resolution or coarse CFD model
- A hierarchy containing more than one of these model types

The exact baseline has not yet been selected.

For a given patient or virtual case, the baseline model produces approximate velocity and pressure behavior:

- Baseline velocity field: `u_0`
- Baseline pressure field: `p_0`

A higher-fidelity reference provides:

- High-fidelity velocity field: `u_HF`
- High-fidelity pressure field: `p_HF`

The discrepancy fields are then defined as:

- Velocity discrepancy: `delta_u = u_HF - u_0`
- Pressure discrepancy: `delta_p = p_HF - p_0`

POD would be applied primarily to these discrepancy fields rather than only to the complete high-fidelity fields.

The corrected prediction would be reconstructed as:

- Corrected velocity: `u_corrected = u_0 + delta_u_predicted`
- Corrected pressure: `p_corrected = p_0 + delta_p_predicted`

The PINN would predict the low-dimensional coefficients associated with the discrepancy POD modes. In the inverse formulation, selected stenosis and physiological parameters would also be inferred from sparse or incomplete observations.

### Intended role of the first-principles baseline

The baseline model should represent physical behavior that does not require a large neural network or full CFD solution.

Depending on the model selected, this may include:

- Conservation of mass
- Approximate conservation of momentum
- Bulk pressure-flow relationships
- Flow resistance
- Approximate velocity profiles
- Dependence on vessel diameter
- Dependence on stenosis severity and stenosis length
- Dependence on flow rate and Reynolds number
- Approximate effects of blood viscosity
- Flow distribution through simplified arterial networks

The baseline is not expected to reproduce every local flow feature near a stenosis.

### Intended role of the discrepancy model

The reduced discrepancy model may account for behavior that is missing, simplified, or poorly represented in the baseline.

Possible unresolved effects include:

- Local flow acceleration through the stenosis throat
- Nonlinear pressure losses
- Flow separation
- Recirculation downstream of the stenosis
- Three-dimensional flow effects
- Vessel curvature
- Stenosis eccentricity
- Near-wall velocity gradients
- Wall shear stress variations
- Non-Newtonian blood behavior
- Boundary-condition uncertainty
- Patient-specific geometric variation
- Errors caused by truncating POD modes
- Differences between idealized computational models and real measurements

The correction term will not automatically be assumed to represent one specific physical mechanism. Its actual behavior must be studied through controlled comparisons and ablation experiments.

## Revised research hypothesis

An idealized first-principles hemodynamic model augmented by a geometry-aware POD-Galerkin PINN discrepancy closure may support rapid forward and inverse stenosis analysis while requiring the learned model to represent only the unresolved portion of the flow.

The project will test whether this architecture can:

- Improve the accuracy of an inexpensive first-principles model
- Maintain substantially lower online cost than high-fidelity CFD
- Require less training data than a neural model trained on complete flow fields
- Recover stenosis parameters from sparse, noisy, or incomplete observations
- Reconstruct velocity and pressure fields within a defined parameter domain
- Provide useful uncertainty estimates when the inverse problem is ambiguous
- Remain stable for unseen but in-range combinations of geometry and physiology

The project will not assume that this approach is superior before comparison with simpler alternatives.

## Mathematical implication of the architecture change

The velocity field is represented as:

`u = u_0 + delta_u`

The pressure field is represented as:

`p = p_0 + delta_p`

The nonlinear convective term in the Navier-Stokes equations depends on the complete corrected velocity field.

Expanding the convective term creates contributions involving:

- The baseline acting on itself
- The baseline acting on the discrepancy
- The discrepancy acting on the baseline
- The discrepancy acting on itself

The discrepancy therefore cannot generally be treated as an independent flow that satisfies the original governing equations by itself.

The physics-informed loss must use one of the following approaches:

1. Evaluate the governing-equation residual using the complete corrected velocity and pressure fields.
2. Derive a consistent perturbation equation that includes all required baseline-discrepancy interaction terms.
3. Construct a reduced equation specifically for the discrepancy using a justified approximation.

The first implementation should avoid omitting cross-terms simply for convenience.

## Discrepancy POD basis

The POD basis for the correction model should be constructed from discrepancy snapshots:

`D_u = U_HF - U_0`

`D_p = P_HF - P_0`

This should be compared with POD applied to the original full-order fields.

The comparison should determine:

- Whether discrepancy modes decay faster than full-field modes
- Whether fewer modes are required for a given reconstruction error
- Whether the discrepancy remains low dimensional across stenosis severity
- Whether severe separation or geometric changes destroy the compact representation
- Whether one global discrepancy basis is adequate
- Whether local or clustered bases become necessary

The discrepancy approach will only be retained as the primary architecture if it provides a measurable benefit.

Changing the baseline model would not invalidate the original high-fidelity CFD data. It would require recomputing:

- Discrepancy snapshots
- Discrepancy POD modes
- Reduced operators
- Trained discrepancy networks

## Boundary-condition considerations

If the baseline and high-fidelity reference satisfy the same boundary conditions, the discrepancy should satisfy compatible homogeneous conditions where appropriate.

For example, if both models satisfy no-slip conditions at the vessel wall, the discrepancy velocity should also be zero at that wall.

If the baseline uses simplified inlet or outlet conditions, the discrepancy model may need to correct those differences.

Possible treatments include:

- Lifting functions
- Penalty terms
- Boundary-compatible POD modes
- Hard constraints
- Explicit boundary losses

The corrected field must not violate conservation or physical wall constraints merely because the discrepancy network reduces the interior field error.

## Revised comparison models

The project should compare the following model hierarchy:

- First-principles baseline alone
- Full-field POD reconstruction
- Full-field POD interpolation
- Full-field POD neural network
- Discrepancy POD reconstruction
- Baseline plus data-only discrepancy neural network
- Baseline plus POD-Galerkin PINN discrepancy model
- Baseline plus constrained discrepancy closure
- High-fidelity CFD or experimental reference

The primary comparisons should determine:

- How much baseline error is removed
- Whether discrepancy POD requires fewer modes
- Whether reduced physics lowers the required amount of training data
- Whether the discrepancy model is more stable than a direct full-field neural model
- Whether the inverse model remains accurate with missing or noisy observations
- Whether the added complexity is justified by measurable gains

## Revised validation metrics

In addition to the previously selected validation metrics, the architecture change introduces several new quantities.

### Baseline error

The error of the first-principles model before applying the correction.

### Corrected-model error

The error after adding the predicted discrepancy.

### Fraction of baseline error removed

A measure of how much of the baseline error is corrected.

### Relative correction magnitude

The norm of the correction compared with the norm of the baseline or final field.

A correction that becomes larger than the baseline over much of the parameter domain may indicate that the selected baseline is not serving a useful purpose.

### Full-field versus discrepancy compression

The POD singular-value decay and reconstruction error should be compared for:

- Full velocity fields
- Full pressure fields
- Velocity discrepancy fields
- Pressure discrepancy fields

### Data efficiency

The number of high-fidelity training cases required to reach a selected accuracy level.

### Stability and physical consistency

The combined prediction should be checked for:

- Mass conservation
- Boundary-condition satisfaction
- Physically reasonable pressure behavior
- Physically reasonable velocity behavior
- Stable behavior at unseen parameter combinations
- Absence of nonphysical oscillations introduced by the correction

## Decision 004 — Investigate a physiologically constrained virtual patient cohort

### Purpose

A large virtual patient cohort may be required to train and test the inverse model over a broad but physically plausible population.

The objective is not to claim coverage of every possible human patient.

The objective is to construct a defined synthetic population that represents plausible combinations of:

- Arterial geometry
- Stenosis characteristics
- Blood-flow conditions
- Blood pressure
- Blood rheology
- Available medical measurements
- Missing-data patterns
- Hemodynamic outputs

The virtual cohort would support:

- Inverse-model training
- Parameter-identifiability studies
- Missing-data experiments
- Noise-robustness experiments
- Uncertainty estimation
- Sensor-placement studies
- Selection of cases for high-fidelity CFD
- Evaluation of the discrepancy closure over a controlled population

## Proposed virtual patient variable groups

### Geometric and anatomical parameters

Potential geometric variables include:

- Healthy vessel diameter
- Vessel segment length
- Stenosis severity
- Stenosis throat diameter
- Nondimensional stenosis length
- Stenosis location
- Stenosis eccentricity
- Stenosis shape
- Vessel curvature
- Vessel taper
- Branch geometry, if later included

The first implementation may use only:

- Stenosis severity
- Nondimensional stenosis length
- Vessel diameter
- Stenosis location

More complex geometry should be introduced only after the basic model is working.

### Physiological parameters

Potential physiological variables include:

- Mean flow rate
- Peak flow rate
- Heart rate
- Systolic blood pressure
- Diastolic blood pressure
- Mean arterial pressure
- Reynolds number
- Blood density
- Blood viscosity
- Carreau-model parameters
- Inlet waveform descriptors
- Downstream resistance
- Downstream compliance
- Other boundary-condition parameters

Systemic blood pressure must not be treated as equivalent to the local pressure drop across a stenosis.

### Measurement variables

Potential observations may include:

- MRI-derived geometry
- MRA-derived geometry
- CTA-derived geometry
- Angiography-derived geometry
- Doppler velocity measurements
- 4D-flow MRI velocity measurements
- Pointwise pressure measurements
- Upstream and downstream pressure measurements
- Flow-rate measurements
- Measurement locations
- Measurement uncertainty
- Acquisition modality
- Spatial and temporal resolution
- Missing-data indicators

### Primary predicted fields

The primary reconstructed hemodynamic fields should initially remain:

- Velocity
- Pressure

Derived outputs should be calculated through post-processing whenever possible.

Potential derived outputs include:

- Pressure drop
- Wall shear stress
- Peak wall shear stress
- Recirculation-zone length or volume
- Flow-reversal regions
- Oscillatory shear index for pulsatile flow
- Estimated stenosis severity
- Estimated stenosis location
- Estimated stenosis geometry
- Prediction uncertainty
- Inference time

## Proposed virtual cohort generation methodology

### Step 1 — Identify and inspect source datasets

Candidate datasets may contain some combination of:

- Anonymized medical imaging
- Vessel geometry
- Blood pressure
- Flow measurements
- Patient-level physiological measurements
- Documented stenosis severity
- Stenosis location
- Other clinical examination outputs

Before using any dataset, the following information must be documented:

- Dataset source
- Dataset version
- License
- Permitted uses
- Redistribution restrictions
- De-identification status
- Data-use agreement requirements
- Available variables
- Units
- Missing values
- Measurement methods
- Population represented
- Known biases
- Scanner or institution effects

A dataset being described as anonymized or de-identified does not automatically mean it can be freely stored, transformed, or redistributed.

### Step 2 — Create a variable and dependency matrix

Every candidate variable should be documented with:

- Variable name
- Physical meaning
- Units
- Source
- Expected range
- Distribution type
- Known correlations
- Physiological constraints
- Whether it is observed, sampled, simulated, or inferred
- Whether it is required or optional
- Whether it is used as an input or output

This matrix will prevent variables from being generated independently when known physiological relationships exist between them.

### Step 3 — Research population ranges and correlations

Large clinical studies, biomedical databases, and published population reports may provide estimates of:

- Typical parameter ranges
- Probability distributions
- Correlations
- Conditional distributions
- Age-dependent relationships
- Vessel-specific relationships
- Disease-dependent relationships
- Measurement uncertainty

These values should be used as priors or constraints rather than as unrelated minimum and maximum values.

The goal should be to estimate a joint population distribution rather than independently sampling every parameter.

### Step 4 — Construct a joint statistical model

A virtual patient should be sampled from a joint distribution representing the relationships between geometry, physiology, disease state, and available measurements.

Possible approaches include:

- Hierarchical Bayesian modeling
- Bayesian networks
- Copula-based models
- Conditional variational models
- Normalizing flows
- Other probabilistic generative models

An interpretable statistical model may be preferable during the first implementation because its correlations and assumptions can be inspected directly.

A simple correlation matrix may be useful as an exploratory tool but is unlikely to represent nonlinear dependencies, conditional relationships, or multimodal populations by itself.

### Step 5 — Apply physiological constraints

Every generated patient should be tested against explicit physical and biological constraints.

Examples include:

- Positive vessel dimensions
- Stenosis severity between zero and complete occlusion
- Stenosis length compatible with the vessel segment
- Stenosis location contained inside the modeled vessel
- Positive viscosity
- Positive density
- Plausible blood-pressure relationships
- Systolic pressure greater than diastolic pressure
- Plausible mean arterial pressure
- Plausible heart rate
- Plausible flow rate
- Consistent Reynolds number
- Consistent diameter and area definitions
- Conservation of mass
- Physically valid resistance and compliance
- Valid geometry without self-intersection
- Boundary conditions compatible with the selected vessel
- No contradictory combinations of lesion severity and geometry

Rejected samples and the reasons for rejection should be logged.

A very high rejection rate would indicate that the statistical model is poorly specified.

### Step 6 — Preserve causal consistency

Hemodynamic outputs should not be sampled independently from arbitrary distributions.

The preferred direction is:

Patient geometry and physiology -> forward hemodynamic model -> simulated observations

Stenosis severity, geometry, rheology, and flow conditions define the patient state.

The first-principles model then produces baseline hemodynamics.

Selected cases receive a higher-fidelity solution.

The discrepancy model learns the difference between the baseline and high-fidelity results.

Measurement noise and missingness are then applied to the simulated observations.

The inverse model attempts to recover the latent stenosis and physiological parameters.

This structure reduces the risk of producing examples where the assigned stenosis severity conflicts with the assigned pressure, velocity, or flow behavior.

### Step 7 — Use a multi-fidelity simulation hierarchy

A high-fidelity CFD simulation should not be required for every synthetic patient.

A possible hierarchy is:

- A very large number of zero-dimensional or one-dimensional model evaluations
- A large number of analytical or axisymmetric cases
- A smaller number of coarse CFD cases
- A limited number of high-fidelity three-dimensional CFD cases
- Experimental or real patient observations where available and authorized

The expensive high-fidelity cases should be selected strategically.

Possible selection methods include:

- Space-filling experimental design
- Latin hypercube sampling
- Uncertainty sampling
- Active learning
- Sampling near regime changes
- Sampling near severe stenosis
- Sampling where the baseline error is largest
- Sampling where the discrepancy model is least certain

### Step 8 — Generate discrepancy training data

For every high-fidelity case:

1. Evaluate the first-principles baseline.
2. Map the baseline result into the high-fidelity field representation.
3. Compute velocity and pressure discrepancies.
4. Store the discrepancy snapshots.
5. Compute POD modes from the discrepancy library.
6. Project the discrepancy onto the POD basis.
7. Store the resulting reduced coefficients.
8. Record all parameters, boundary conditions, software versions, and solver settings.

The original high-fidelity fields should also be preserved so that full-field and discrepancy-based approaches can be compared fairly.

### Step 9 — Model missing observations

Real patient datasets frequently contain incomplete measurements.

The inverse model should receive:

- The observed values
- A binary or categorical mask identifying which values are present
- Measurement locations
- Measurement uncertainty where known

Initial tests may remove values under controlled random conditions.

Later tests should reproduce realistic missingness patterns from the source dataset.

Missing measurements should not automatically be replaced with a single mean value.

Possible future approaches include:

- Mask-aware neural networks
- Conditional probabilistic inference
- Learned imputation
- Multiple imputation
- Latent-variable models
- Generative conditional models

The first implementation method has not yet been selected.

### Step 10 — Add realistic measurement noise

Noise should be based on the type of observation.

Examples include:

- Pressure measurement uncertainty
- Doppler velocity uncertainty
- MRI spatial-resolution effects
- MRI temporal-resolution effects
- Segmentation uncertainty
- Vessel-diameter measurement uncertainty
- Flow-waveform uncertainty

A single generic Gaussian noise level should not automatically be applied to every measurement type.

### Step 11 — Prevent data leakage

If patient-level source data are used, the train, validation, and test split must occur before synthetic expansion.

Synthetic records derived from the same patient must not appear in both the training and testing groups.

Otherwise, the model could indirectly memorize patient-specific patterns and produce artificially favorable validation results.

Possible split levels include:

- Patient-level split
- Institution-level split
- Dataset-level split
- Geometry-family split
- Time-based split, where appropriate

### Step 12 — Validate the virtual cohort

The virtual cohort should be compared with its source population and with independent physiological knowledge.

Validation should include:

- Univariate distribution comparison
- Pairwise correlation comparison
- Multivariate dependence comparison
- Conditional distribution comparison
- Subgroup coverage
- Coverage of severe and uncommon cases
- Physiological constraint pass rate
- Duplicate and near-duplicate detection
- Comparison of predicted pressure and flow behavior with published ranges
- Comparison of model outputs with independent CFD or experimental cases
- Train-on-synthetic and test-on-held-out-real evaluation, where permitted
- Sensitivity to the selected generative model

Matching individual variable ranges is not sufficient if the generated relationships between variables are unrealistic.

### Step 13 — Evaluate synthetic-data privacy

Synthetic data generated from patient-level records are not automatically free of privacy risk.

Before release, the following risks should be considered:

- Exact duplication
- Near duplication
- Memorization of rare patients
- Membership inference
- Attribute disclosure
- Reconstruction of original records
- Dataset-specific redistribution restrictions

A cohort generated only from published aggregate statistics would present a different and generally simpler privacy situation than a model trained directly on individual patient records.

## What the virtual cohort can support

A well-constructed virtual cohort may support:

- Broad parameter-space exploration
- Controlled comparison of model architectures
- Identifiability studies
- Sensor-placement studies
- Testing of missing measurements
- Testing of noisy measurements
- Training of inverse models
- Training of discrepancy closures
- Active selection of high-fidelity simulations
- Uncertainty estimation
- Reproducible benchmark development

## What the virtual cohort cannot establish by itself

Synthetic data cannot by itself:

- Prove clinical accuracy
- Represent every possible patient
- Add biological relationships absent from the source information
- Remove bias from an unrepresentative source cohort
- Guarantee generalization to another institution or population
- Replace independent real-data validation
- Make an underdetermined inverse problem uniquely identifiable
- Correct an invalid first-principles model automatically
- Establish that the learned discrepancy has a unique physical meaning

## Main risks introduced by the revised direction

### Baseline model is too inaccurate

If the baseline is poor across most of the vessel, the discrepancy model may need to reconstruct nearly the entire flow field.

This would eliminate much of the intended benefit.

### Discrepancy is not low dimensional

Localized jets, separation zones, and geometric variation may require many POD modes.

Local or clustered POD may be required.

### Baseline and high-fidelity fields are not directly compatible

A zero-dimensional or one-dimensional baseline must be mapped into a multidimensional field before subtraction.

The mapping may introduce artificial discrepancy structure.

### Incorrect split of the governing equations

Omitting nonlinear interaction terms between the baseline and discrepancy could produce a physically inconsistent PINN loss.

### Correction overwhelms the baseline

If the correction becomes comparable to or larger than the baseline over much of the parameter domain, the decomposition may not be useful.

### Boundary-condition mismatch

The correction may introduce wall, inlet, or outlet artifacts if baseline and reference boundary conditions are inconsistent.

### Inverse non-identifiability

Different combinations of stenosis geometry, rheology, flow rate, and downstream resistance may produce similar sparse observations.

### Correlation distortion in the virtual cohort

Independent or poorly constrained sampling may create false relationships or remove real ones.

### Synthetic-to-real gap

The inverse model may learn artifacts specific to the simulator or cohort generator.

### Data leakage

Synthetic expansion performed before patient-level splitting may contaminate model evaluation.

### Privacy leakage

A patient-trained generator may reproduce identifying or rare combinations.

### Excessive project scope

Simultaneously developing a virtual cohort, baseline model, discrepancy ROM, inverse network, uncertainty model, and interface may be too broad for one initial implementation.

The work should remain stage-gated.

## Revised implementation sequence

1. Identify candidate public CFD, imaging, and physiological datasets.
2. Construct a literature and dataset matrix.
3. Define the first virtual-patient variable schema.
4. Select two candidate first-principles baseline models.
5. Test those baselines against a small number of known stenosis cases.
6. Determine how baseline outputs will be represented on the high-fidelity field domain.
7. Compute a small pilot discrepancy library.
8. Compare full-field and discrepancy POD singular-value decay.
9. Retain the discrepancy architecture only if it provides a measurable benefit.
10. Implement a forward discrepancy surrogate.
11. Introduce the Galerkin-informed discrepancy loss.
12. Formulate a simple inverse problem using known synthetic observations.
13. Add controlled noise.
14. Add controlled missing observations.
15. Expand the virtual cohort only after the initial model is verified.
16. Add more complex geometry and patient-informed distributions later.
17. Build a frontend only after the underlying method is validated.

## Immediate next actions

1. Update the Research Project Definition Document to reflect the architecture pivot.
2. Preserve version 0.1 as a record of the original project formulation.
3. Define the terminology used for baseline, discrepancy, closure, and correction.
4. Create a table of candidate first-principles models.
5. Begin a public dataset and literature search.
6. Define the initial virtual-patient variables and units.
7. Separate variables into sampled, observed, simulated, inferred, and derived categories.
8. Define initial physiological constraints.
9. Select a small benchmark suitable for testing baseline subtraction.
10. Compare full-field POD and discrepancy POD before developing the inverse network.

## Decision log

### Decision 003 — Adopt a baseline-plus-discrepancy architecture

The project will investigate a first-principles hemodynamic model coupled with a reduced POD-Galerkin PINN correction.

The learned model will focus primarily on unresolved discrepancy rather than reproducing the complete bulk flow from scratch.

The full-field POD-Galerkin PINN will remain a comparison model.

### Decision 004 — Investigate a physiologically constrained virtual patient cohort

The project will investigate a virtual patient methodology based on joint statistical modeling, published physiological ranges, authorized anonymized data where available, and explicit biophysical constraints.

Hemodynamic outputs will be generated through a model hierarchy rather than independently assigned.

### Decision 005 — Preserve uncertainty about the revised approach

The discrepancy model will not be assumed to be lower dimensional, more accurate, more stable, or more data efficient before testing.

Negative findings will be documented and may result in returning to a full-field reduced model or narrowing the project to a controlled benchmark study.