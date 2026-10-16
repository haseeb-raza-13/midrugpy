# QuantumDrug

**An open-source, quantum-ready Python framework for AI-assisted small-molecule drug discovery.**

QuantumDrug aims to connect molecular data preparation, chemical descriptors, protein structures, predictive modeling, and reproducible evaluation in one modular research workflow. Its central principle is to build a useful classical system first, then measure whether optional quantum methods add value.

> **Project status: planning / pre-implementation.** This repository currently contains this README and a GPLv3 license. The package, data adapters, models, tests, notebooks, and command-line tools described below have not yet been implemented. QuantumDrug is a working title; software-name and trademark checks remain a release task.

## Contents

- [Purpose and intended audience](#purpose-and-intended-audience)
- [How the workflow will work](#how-the-workflow-will-work)
- [Planned capabilities](#planned-capabilities)
- [Data sources and scientific data handling](#data-sources-and-scientific-data-handling)
- [Modeling and evaluation](#modeling-and-evaluation)
- [What quantum-ready means](#what-quantum-ready-means)
- [Proposed architecture](#proposed-architecture)
- [Development roadmap](#development-roadmap)
- [Getting started and contributing](#getting-started-and-contributing)
- [Limitations and future scope](#limitations-and-future-scope)
- [License and project basis](#license-and-project-basis)

## Purpose and intended audience

Drug-discovery research often requires separate tools for collecting measurements, cleaning chemical structures, calculating features, inspecting proteins, and evaluating predictions. QuantumDrug intends to make those steps reusable, traceable, and easier to combine.

The project is intended for cheminformatics and bioinformatics researchers, computational drug-discovery teams, students learning molecular machine learning, and researchers comparing classical and quantum approaches on well-defined tasks.

The initial focus is **target-specific small-molecule bioactivity prediction**. A first benchmark should use one protein target, one defined experimental endpoint, and a documented activity threshold where classification is appropriate. The initial target, endpoint, dataset, and threshold have yet to be selected.

The intended output is a reproducible set of research hypotheses: curated datasets, molecular features, evaluated models, and candidate rankings with their limitations. Experimental validation remains essential before claims about biological efficacy or safety.

## How the workflow will work

```text
Compounds + bioactivity records + target metadata
                       |
                       v
Structure standardization and assay quality control
                       |
                       v
Molecular descriptors and fingerprints
                       |
                       +----- Optional protein / pocket analysis
                       |      and docking evidence
                       v
Classical predictive models
                       |
                       +----- Optional graph or quantum experiments
                       |
                       v
Evaluation, uncertainty, and candidate prioritization
                       |
                       v
Reproducible reports and independent experimental testing
```

| Stage | Planned work | Expected research output |
| --- | --- | --- |
| 1. Acquire data | Retrieve compounds, assays, target metadata, sequences, and structures | Source records with identifiers and provenance |
| 2. Check quality | Standardize molecules and inspect duplicates, units, missing values, qualifiers, and conflicts | Curated dataset and quality report |
| 3. Calculate features | Compute chemical properties, descriptors, and fingerprints | Versioned feature table |
| 4. Analyze structures | Inspect proteins and binding sites; optionally dock ligands and extract interactions | Structural context and supporting evidence |
| 5. Train models | Establish classical baselines, then evaluate optional extensions | Models with documented training and evaluation settings |
| 6. Prioritize candidates | Combine activity estimates, uncertainty, molecular properties, and relevant structural evidence | Ranked hypotheses with supporting information |
| 7. Validate | Assess generalization and document limitations | Reproducible benchmark and candidates for independent testing |

## Planned capabilities

### Molecular preprocessing and descriptors

RDKit is the proposed primary cheminformatics dependency. The descriptor engine will start with SMILES parsing, molecular validation, canonical structure output, and selected physicochemical properties.

- Lipinski properties: molecular weight, calculated LogP, hydrogen-bond donors, and hydrogen-bond acceptors.
- Explicit rule-violation counts using the proposal's thresholds: molecular weight > 500, LogP > 5, donors > 5, and acceptors > 10.
- Additional RDKit descriptors, including topological polar surface area, and Morgan fingerprints.
- Optional PaDEL descriptors and fingerprint families through a separate adapter.
- Optional 3D descriptors when a suitable molecular conformer is available.
- Feature filtering, missing-value handling, descriptor provenance, and software-version tracking.

The proposal's prototype defines a strict zero-violation flag. Any public API should document that convention explicitly. Lipinski properties are drug-likeness heuristics; they do not establish efficacy or safety. PaDEL descriptors describe molecules, while protein and binding-pocket features require separate calculations.

### Protein structures and docking

The structure layer is planned to retrieve structures by PDB identifier and accept local PDB/mmCIF files. It should record whether a structure is experimental or predicted, along with chains, ligands, cofactors, missing residues, and available quality or confidence information.

Later capabilities include protein preparation, known or user-specified binding sites, optional AutoDock Vina integration, and supported protein-ligand interaction analysis such as hydrogen bonds, hydrophobic contacts, salt bridges, and aromatic interactions.

Docking engines will remain optional external dependencies. Scores and poses provide prioritization evidence; they are not direct measurements of binding affinity or biological efficacy.

### Reporting and reproducibility

Planned reports should connect results to source records, curation decisions, descriptor settings, model parameters, data splits, random seeds, and software versions. Example notebooks, tests, and model cards should explain intended use, evaluation data, uncertainty, and known failure modes.

## Data sources and scientific data handling

These are proposed integrations, not currently available adapters.

| Source | Intended role | Context to preserve |
| --- | --- | --- |
| ChEMBL | Bioactivity measurements, assays, targets, and compound structures | Assay context, target identity, endpoint, units, and measurement qualifiers |
| PubChem | Compound identifiers, chemical structures, and metadata | Identifier mappings and source provenance; chemical metadata alone is not an assay-curated activity dataset |
| RCSB Protein Data Bank | Experimental macromolecular structures and complexes | Chains, ligands, structure quality, and experimental context |
| AlphaFold Protein Structure Database | Predicted protein structures | Prediction status and confidence information |
| NCBI | Sequences, genes, pathogen records, and biological datasets | Source-specific identifiers, provenance, and access rules |

AMBER is a molecular-simulation and force-field ecosystem, rather than a general protein-structure repository. Any future NVIDIA or university-specific integration needs a concrete tool, dataset, or database before it can become an adapter.

### Bioactivity curation rules

The data layer should retain the original measurement, units, endpoint, assay identifier, target identity, source, and inequality qualifiers such as `<` and `>`. Conflicting records should be flagged for review instead of silently merged.

IC50, Ki, Kd, and EC50 represent different experimental quantities and contexts. They must not be pooled indiscriminately into one label or regression target.

For compatible concentration measurements converted to molar units:

```text
pC = -log10(C in mol/L)
1 micromolar = 10^-6 mol/L -> pC = 6
```

Conversions must retain original values and metadata. Endpoint-specific names, such as pIC50, should be used when applicable. Censored values require an explicit handling policy rather than being treated automatically as exact measurements.

## Modeling and evaluation

### Classical baselines first

Initial models will use molecular descriptors and fingerprints for classification or regression. Proposed baselines include logistic regression for classification, random forests, gradient boosting, and support vector machines with task-appropriate variants.

Graph neural networks are a later extension, after the dataset and benchmark are reliable and sufficient data are available. They would learn representations from atom and bond graphs.

### Evaluation standards

| Area | Planned validation |
| --- | --- |
| Descriptor correctness | Compare known molecules against established software outputs; test invalid inputs and edge cases |
| Dataset quality | Inspect duplicates, endpoint consistency, units, missing values, conflicts, and provenance |
| Classification | Report suitable precision, recall, F1, ROC-AUC, and PR-AUC, with attention to class balance |
| Regression | Report MAE, RMSE, and appropriate uncertainty or calibration checks |
| Generalization | Use scaffold-based splits and, where feasible, external or time-based test sets |
| Quantum comparison | Use identical splits and account for feature encoding, runtime, measurement overhead, and hardware noise where applicable |

Random splits alone can place close chemical analogues in both training and test sets, overstating performance on new scaffolds. Preprocessing and feature selection must also be designed to avoid test-set leakage. Benchmark reports should record split definitions, random seeds, hyperparameters, and limitations so results can be reproduced.

No model performance, discovered candidates, or quantum advantage has been demonstrated by this repository yet.

## What quantum-ready means

Ordinary use is intended to work without quantum hardware or quantum libraries. A provider-neutral extension layer would let Python orchestrate optional quantum simulators or hardware alongside classical processing.

The first proposed quantum experiment is a small feature set encoded into quantum circuits for a quantum kernel or hybrid classifier, evaluated against strong classical baselines on the same task and splits. Qiskit Machine Learning and PennyLane are potential integrations; neither has been selected or installed as a project dependency.

Longer-term research directions include electronic-structure calculations for selected small molecular systems and narrowly defined hybrid optimization problems. These are exploratory directions beyond the first benchmark.

Quantum experiments must account for state preparation, qubit limits, circuit depth, measurement cost, noise, and communication overhead. A successful simulator experiment does not establish quantum computational advantage. Predictive quality and total computational cost both matter, and quantum methods may perform worse than classical alternatives.

## Proposed architecture

The following tree is a design target. **Only `Readme.md` and `LICENSE` currently exist.**

```text
.
|-- Readme.md
|-- LICENSE
|-- pyproject.toml                    # Planned packaging and dependency configuration
|-- src/
|   `-- quantumdrug/
|       |-- config.py
|       |-- data/                     # Source-specific adapters
|       |   |-- chembl.py
|       |   |-- pubchem.py
|       |   `-- pdb.py
|       |-- preprocessing/
|       |   |-- molecules.py
|       |   |-- bioactivity.py
|       |   `-- quality.py
|       |-- descriptors/
|       |   |-- lipinski.py
|       |   |-- rdkit_features.py
|       |   `-- padel.py
|       |-- structures/
|       |   |-- preparation.py
|       |   |-- binding_sites.py
|       |   `-- interactions.py
|       |-- docking/
|       |   `-- vina.py
|       |-- models/
|       |   |-- classification.py
|       |   |-- regression.py
|       |   `-- evaluation.py
|       |-- quantum/
|       |   |-- interfaces.py
|       |   |-- kernels.py
|       |   `-- hybrid.py
|       `-- reporting/
|           `-- results.py
`-- tests/
    |-- test_descriptors.py
    |-- test_preprocessing.py
    `-- test_models.py
```

Data adapters should be separate from preprocessing, features, and models. Docking, PaDEL, and quantum integrations should be optional so that the core remains usable with a smaller dependency set. Public functions will need defined inputs, outputs, error behavior, and tests before being documented as available APIs.

## Development roadmap

All phases are planned. Timing is indicative elapsed development time from project kickoff, with overlapping phases; it is not a release commitment.

| Phase | Indicative timing | Deliverable |
| --- | --- | --- |
| 1. Descriptor library | Weeks 1-3 | Package skeleton, Lipinski properties, RDKit features, tests, CSV export, and documentation |
| 2. Bioactivity integration | Weeks 3-6 | ChEMBL adapter, endpoint normalization, and one curated target-specific dataset |
| 3. Classical prediction | Weeks 6-10 | Baseline models, robust splits, evaluation metrics, and uncertainty reporting |
| 4. Structure integration | Weeks 10-16 | PDB retrieval, preparation, optional docking, and interaction analysis |
| 5. Quantum research | Weeks 16-24+ | Small quantum/hybrid benchmark with a documented classical comparison |
| 6. Public release | Iterative | Packaging, continuous integration, tutorials, examples, model cards, dependency review, and reproducible releases |

### First implementation milestone

Start with the descriptor engine and a clean package structure. The first proposed function is `calculate_lipinski(smiles)`, returning canonical SMILES, molecular weight, LogP, donor and acceptor counts, violation count, a documented rule-of-five flag, and topological polar surface area. Invalid SMILES should produce a clear error.

Completion should include descriptor checks against known reference outputs, preprocessing tests, CSV export, and a runnable example with explicit dependency requirements. After this foundation, add one bioactivity source and establish a target-specific classical benchmark before integrating structures or quantum experiments.

## Getting started and contributing

There is no installable QuantumDrug package, supported Python-version declaration, dependency manifest, runnable example, or test suite in this repository yet. Installation commands and API usage will be added once they can be verified against the implementation.

For now, contributors can use this README as the project brief and work toward the first milestone. Useful contributions include molecular preprocessing, descriptor reference cases, assay-curation policies, reproducible benchmarking, documentation, and carefully evaluated optional integrations.

Before implementing a feature, define its research use case, inputs and outputs, dependencies, failure behavior, and validation approach. Contributions should preserve source provenance, document any data-license requirements, and include meaningful tests where applicable. Dataset and dependency licenses require their own review; the repository's software license does not automatically cover external data or tools.

Early project decisions still include the initial protein target and endpoint, dataset selection, supported Python versions, dependency versions, API conventions, and release scope.

## Limitations and future scope

Public datasets may contain assay heterogeneity, duplicate compounds, conflicting measurements, and selection bias. Protein preparation, protonation states, cofactors, missing residues, and structural uncertainty can materially change results. Reports should make these uncertainties visible.

This is research software in planning. Drug-likeness rules, activity predictions, and docking scores cannot establish clinical usefulness, efficacy, or safety. Candidate claims require appropriate independent biological experiments.

A future vaccine-research module could address pathogen sequences, antigen characterization, epitope prediction, sequence conservation, and population coverage. It could share retrieval and reproducibility utilities, but would require distinct labels, endpoints, evaluation, and experimental validation. It is outside the initial small-molecule scope; a predicted epitope is not automatically an effective vaccine antigen.

Potential research contributions include a reproducible end-to-end platform, a rigorous classical-versus-quantum benchmark, or a new hybrid method supported by controlled experiments. Integrating existing tools is useful engineering, but scientific novelty must be demonstrated separately.

## License and project basis

The repository includes the **GNU General Public License, version 3**. See [LICENSE](LICENSE) for the full terms.

This README is based on *QuantumDrug: An Open-Source, Quantum-Ready Python Framework for AI-Assisted Drug Discovery - Project Concept & Development Roadmap*, supplied as `QuantumDrug_Project_Proposal.pdf`. The proposal describes an exploratory concept; this README distinguishes those plans from the repository's current implementation status. The source PDF is not currently included in the repository.
