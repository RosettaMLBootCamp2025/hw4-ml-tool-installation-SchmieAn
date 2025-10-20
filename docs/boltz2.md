# Boltz-2

Boltz-2 ([paper](https://doi.org/10.1101/2025.06.14.659707), [code](https://github.com/jwohlwend/boltz)) is a biomolecular foundation model that jointly models complex structures and binding affinities. It's the first deep learning model to approach the accuracy of physics-based free-energy perturbation (FEP) methods while running 1000x faster.

Boltz-2 is useful for structure prediction of biomolecular complexes AND binding affinity prediction, making it valuable for drug discovery and protein design. It can predict structures for proteins, nucleic acids, small molecules, and covalent modifications.

The goal of this homework assignment is to install Boltz-2 on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software. We'll be creating virtual environments using Conda/Mamba.

**Note**: It's recommended to install Boltz in a fresh Python environment.

## Installation Steps

**Option 1: Install from PyPI (recommended)**:
```bash
pip install boltz[cuda] -U
```

**Option 2: Install from GitHub for daily updates**:
```bash
git clone https://github.com/jwohlwend/boltz.git
cd boltz
pip install -e .[cuda]
```

**For CPU-only or non-CUDA GPUs**: Remove `[cuda]` from the commands above (note: CPU version is significantly slower).

## Testing the Installation

Create a test YAML file `test_input.yaml`:

```yaml
version: 1
sequences:
  - protein:
      id: [A, B]
      sequence: MKTVRQERLKSIVRILERSKEPVSGAQLAEELSVSRQVIVQDIAYLRSLGYNIVATPRGYVLAGG
```

Run prediction:
```bash
boltz predict test_input.yaml --use_msa_server
```

This will predict the structure and save results in the output directory.

## Input Format

Boltz uses YAML files to describe biomolecules and properties to predict. For detailed input format information, see the [prediction instructions](https://github.com/jwohlwend/boltz/blob/main/docs/prediction.md).

## Binding Affinity Prediction

Boltz-2 provides two affinity predictions:

1. **`affinity_probability_binary`** (0-1 scale):
   - Use for: Hit discovery, detecting binders from decoys
   - Represents: Probability that ligand is a binder

2. **`affinity_pred_value`** (log10(IC50) in μM):
   - Use for: Hit-to-lead and lead optimization
   - Represents: Specific binding affinity for comparison

## MSA Server Authentication

When using `--use_msa_server` with servers requiring authentication, provide credentials:

```bash
export BOLTZ_MSA_TOKEN="your_token_here"
boltz predict input.yaml --use_msa_server
```

See the [prediction documentation](https://github.com/jwohlwend/boltz/blob/main/docs/prediction.md) for more details.

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=boltz
#SBATCH --gpus=1
#SBATCH --time=04:00:00
#SBATCH --mem=32G

module load cuda/12.1

# Activate environment
source ~/.bashrc
# or: mamba activate boltz_env

# Run prediction
boltz predict my_complex.yaml --use_msa_server --out_dir results/
```

## Performance Notes

- **GPU**: Boltz runs efficiently on recent NVIDIA GPUs
- **CPU**: Functional but significantly slower than GPU
- **Speed**: Approximately 1000x faster than FEP methods for affinity prediction

## Example Use Cases

**Structure prediction only**:
```bash
boltz predict structure.yaml
```

**Structure + Affinity prediction**:
```yaml
# In your YAML file, specify affinity prediction
version: 1
sequences:
  - protein:
      id: A
      sequence: MKTVRQERLK...
  - ligand:
      id: L
      smiles: "CC(C)CC1=CC=C(C=C1)C(C)C"
properties:
  - affinity
```

## Troubleshooting

**Installation issues**: Ensure you're in a fresh environment and have compatible CUDA version.

**MSA server errors**: Check authentication credentials and server availability.

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
