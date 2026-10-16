# MiDrugPy

**An open-source Python framework for AI-assisted drug discovery, with optional quantum-computing research capabilities.**

MiDrugPy aims to bring molecular data preparation, chemical property calculation, and predictive modeling into one reproducible workflow. It is intended for researchers, students, and contributors working in cheminformatics, bioinformatics, and molecular machine learning.

The project follows a classical-first approach: build reliable tools for small-molecule research, then evaluate whether quantum and hybrid methods offer measurable benefits.

## Project status

QuantumDrug is at the planning stage. This repository currently contains project documentation and a license; the Python package has not yet been implemented. QuantumDrug is a working name.

## Planned features

- Retrieve and curate public chemical and bioactivity data, starting with ChEMBL.
- Validate molecular structures and calculate Lipinski properties, RDKit descriptors, and molecular fingerprints.
- Train and evaluate classical models for target-specific bioactivity prediction.
- Add optional protein-structure analysis, molecular docking, and interaction analysis.
- Explore quantum kernels and hybrid models through optional integrations.
- Provide reproducible examples, tests, and reports that document data sources and model limitations.

The initial milestone is a tested RDKit-based descriptor engine. Bioactivity integration and classical modeling will follow, with structure-based tools and quantum experiments added later.

## Getting started

Installation instructions and runnable examples will be added when the first working package is available. No quantum hardware will be required for the core workflow.

## Contributing

Contributions to code, tests, documentation, and scientific validation are welcome. Before starting a substantial change, open an issue to discuss its scope and how it will be evaluated.

## Research scope

QuantumDrug is intended to support research and candidate prioritization. Molecular properties, docking scores, and model predictions do not establish drug safety or efficacy; independent experimental validation is essential. Quantum advantage will be evaluated rather than assumed.

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).
