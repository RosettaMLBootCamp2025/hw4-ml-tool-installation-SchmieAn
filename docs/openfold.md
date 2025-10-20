# OpenFold

OpenFold ([paper](https://www.biorxiv.org/content/10.1101/2022.11.20.517210v1), [code](https://github.com/aqlaboratory/openfold)) is a faithful, trainable PyTorch reproduction of DeepMind's AlphaFold2. It achieves performance comparable to AlphaFold2 and provides a fully open-source implementation for protein structure prediction.

OpenFold is useful for protein structure prediction with full transparency into the model architecture and training process. It's particularly valuable for researchers who want to understand, modify, or retrain structure prediction models.

The goal of this homework assignment is to install OpenFold on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software.

**Note**: OpenFold installation can be complex. Please refer to the official documentation at [openfold.readthedocs.io](https://openfold.readthedocs.io/en/latest/) for the most up-to-date installation instructions.

## Recommended Installation Method

The easiest way to install OpenFold on an HPC is to follow the official documentation:

1. Visit the OpenFold documentation: [https://openfold.readthedocs.io](https://openfold.readthedocs.io/en/latest/)

2. Follow the installation instructions for your system

3. The documentation provides detailed guidance for:
   - Environment setup
   - Dependency installation
   - Model weight downloads
   - Running inference
   - Training (if desired)

## Key Requirements

- Python 3.7+
- PyTorch
- CUDA-capable GPU
- Significant disk space for databases (if using MSAs)

## Basic Installation Overview

While exact commands may change, the general process involves:

1. Clone the repository:
```bash
git clone https://github.com/aqlaboratory/openfold.git
cd openfold
```

2. Set up the conda environment:
```bash
# Follow instructions from official docs
conda env create -f environment.yml
conda activate openfold
```

3. Install OpenFold:
```bash
# Follow instructions from official docs
python setup.py install
```

4. Download model weights:
```bash
# Scripts provided in the repository
bash scripts/download_openfold_params.sh openfold/resources
```

## HPC-Specific Notes

**Database Requirements**: If using MSAs, OpenFold requires access to large sequence databases (similar to AlphaFold2). These may already be available on your HPC system. Check with your system administrator.

**GPU Requirements**: Structure prediction requires CUDA-capable GPUs. Request appropriate GPU resources when submitting jobs.

**Docker/Singularity**: If OpenFold provides Docker containers, remember that most academic HPCs use Singularity/Apptainer instead of Docker.

## Testing Your Installation

Once installed, test with a simple prediction:

```bash
# Example command (exact syntax from official docs)
python run_pretrained_openfold.py \
    --fasta_paths example.fasta \
    --output_dir predictions/
```

Consult the official documentation for exact command syntax and options.

## Why OpenFold?

- **Transparency**: Full access to model architecture and training code
- **Reproducibility**: Can retrain models with custom data
- **Research**: Understand how structure prediction models work
- **Customization**: Modify model architecture for specific applications

## Documentation and Support

For the most accurate and up-to-date information:

- **Official Documentation**: [https://openfold.readthedocs.io](https://openfold.readthedocs.io/en/latest/)
- **GitHub Repository**: [https://github.com/aqlaboratory/openfold](https://github.com/aqlaboratory/openfold)
- **Original README**: Available in the repository

If you encounter issues, consult the official documentation first, then contact Ian Anderson at icanderson@ucdavis.edu.
