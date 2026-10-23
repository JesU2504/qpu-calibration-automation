> **Status: project proposal — no implementation is committed in this repository.**
> The components, features, benchmarks, and visualizations below describe a
> proposed design. They are not completed work or measured results.

# qpu-calibration-automation

Why This Project Exists
IQM's QPU Performance Group internship and junior software roles explicitly list QuTiP, JAX, and calibration experiments as core requirements — skills that are rarely seen together in student portfolios. Every QPU in the world must be calibrated continuously. This project simulates that calibration workflow end-to-end, demonstrating not just quantum theory knowledge but a deep understanding of the operational reality of running superconducting quantum hardware.

What You Are Building
An automated calibration pipeline that simulates the full cycle of experiments used to characterize and optimize a superconducting transmon qubit. The system models the qubit physics in QuTiP, runs simulated calibration experiments, then uses JAX-powered gradient descent to find the optimal pulse parameters that maximize gate fidelity. A drift simulation module models how qubit parameters shift over time, replicating the real operational challenge of maintaining QPU performance.

Repository Structure
README.md
Overview, architecture diagram, calibration cycle explanation, result plots
qubit_model/
QuTiP transmon Hamiltonian: T1, T2, drive terms, collapse operators
experiments/
Four calibration experiments: Rabi, Ramsey, amplitude cal, DRAG sweep
optimizer/
JAX-based gradient descent for pulse parameter optimization
drift_sim/
Thermal drift model: time-varying qubit parameters and recalibration triggers
reporter/
Calibration report generator: optimal parameters, achieved fidelity, drift plots
tests/
Unit and integration tests (pytest + GitHub Actions CI)
notebooks/
Interactive Jupyter walkthroughs for each calibration experiment

Core Components

1. Transmon Qubit Model (QuTiP)
A realistic open quantum systems model of a superconducting transmon qubit using QuTiP's master equation solver. The model captures the key physical properties that calibration experiments are designed to measure.
	•	System Hamiltonian: charge qubit with anharmonicity, drive term
	•	Collapse operators: T1 relaxation, T2 dephasing (configurable values)
	•	Drive: microwave pulse modeled as time-dependent Hamiltonian term
	•	Leakage: includes the |2⟩ state to model transmon anharmonicity effects

2. Calibration Experiments
Four Experiments Implemented
	•	Rabi oscillation — sweeps drive amplitude to find the π-pulse amplitude (single-qubit gate flip)
	•	Ramsey detuning — sweeps free evolution time to measure qubit frequency and T2*
	•	Amplitude calibration — fine-tunes the π-pulse amplitude around the Rabi estimate
	•	DRAG parameter sweep — optimizes the Derivative Removal via Adiabatic Gate coefficient to minimize leakage to |2⟩

3. JAX Optimization Engine
Uses JAX's automatic differentiation to compute the gradient of gate fidelity with respect to pulse parameters, enabling fast and reliable parameter optimization. This is the same computational approach used in production quantum control systems.
	•	Objective function: process fidelity of the driven gate vs ideal target
	•	Parameters optimized: drive amplitude, frequency detuning, DRAG coefficient, pulse duration
	•	Optimizer: Adam with JAX jit-compilation for fast evaluation
	•	Convergence tracking: fidelity vs optimization iteration plotted for each experiment

4. Thermal Drift Simulation
This is the most distinctive component. Real QPUs drift — qubit frequencies shift by kilohertz over hours due to thermal fluctuations, charge noise, and flux noise. This module simulates that drift and shows how frequently recalibration must be triggered to maintain target fidelity.
	•	Drift model: Ornstein-Uhlenbeck process on qubit frequency and T1/T2 values
	•	Configurable drift rate and correlation time (matching published IQM device specs)
	•	Recalibration trigger: automated detection when fidelity drops below threshold
	•	Output: time-series plot of qubit fidelity with recalibration events marked

5. Calibration Report
Generates a structured calibration report summarizing optimal pulse parameters, achieved gate fidelities, and drift sensitivity — mirroring the kind of output a QPU operator would inspect after each calibration cycle.

What to Measure
Gate fidelity
Achieved fidelity for X, Y, and CZ gates after optimization
Optimization convergence
Fidelity vs iteration number for each experiment
Drift sensitivity
How quickly fidelity degrades as qubit parameters drift
Recalibration frequency
How often the pipeline must be re-run at different drift rates
DRAG effectiveness
Leakage probability with and without DRAG optimization

What Skills This Demonstrates to IQM
Direct Match to Their Internship Requirements
	•	QuTiP fluency: mesolve, Qobj, time-dependent Hamiltonians — explicitly listed in their posting
	•	JAX usage for numerical optimization — specifically called out in their requirements
	•	Understanding of calibration experiments: Rabi, Ramsey, RB — their QPU Performance Group's daily work
	•	Software engineering practices: unit tests, CI/CD, documentation — required for all their roles
	•	Understanding of QPU operational reality (drift, recalibration) beyond textbook theory
