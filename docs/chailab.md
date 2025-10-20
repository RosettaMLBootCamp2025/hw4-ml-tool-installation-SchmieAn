# Chai-1

Chai-1 ([paper](https://www.biorxiv.org/content/10.1101/2024.10.10.615955), [code](https://github.com/chaidiscovery/chai-lab)) is a multi-modal foundation model for molecular structure prediction that achieves state-of-the-art performance across diverse benchmarks. Chai-1 enables unified prediction of proteins, small molecules, DNA, RNA, glycosylations, and more.

Chai-1 is useful for predicting complex biomolecular structures including protein-ligand complexes, protein-nucleic acid complexes, and multi-component assemblies. It can handle modified residues and incorporate experimental restraints.

The goal of this homework assignment is to install Chai-1 on your HPC cluster.

## Preparation Work

**Requirements**:
- Linux operating system
- Python 3.10 or later
- GPU with CUDA and bfloat16 support
- Recommended: A100 80GB, H100 80GB, or L40S 48GB
- Also works on: A10, A30, RTX 4090

## Installation Steps

1. Create a conda environment with Python 3.10+:
```bash
mamba create -n chailab python=3.11
mamba activate chailab
```

2. Install Chai-1:
```bash
pip install chai_lab==0.6.1
```

Or for the latest development version:
```bash
pip install git+https://github.com/chaidiscovery/chai-lab.git
```

## Testing the Installation

Create a test FASTA file `test.fasta`:
```
>protein|name=example
MKTVRQERLKSIVRILERSKEPVSGAQLAEELSVSRQVIVQDIAYLRSLGYNIVATPRGYVLAGG
```

Run prediction:
```bash
chai-lab fold test.fasta output_folder/
```

By default, this generates 5 sample predictions using embeddings without MSAs.

## Using MSAs for Better Performance

For improved performance, use MSAs via the ColabFold server:

```bash
chai-lab fold --use-msa-server --use-templates-server input.fasta output_folder/
```

**Note**: This uses the public ColabFold MMseqs2 server, which is a shared resource. Please be considerate of usage.

## For HPC Internal ColabFold Server

If your HPC has an internal ColabFold server:

```bash
chai-lab fold --use-msa-server \
    --msa-server-url "https://internal.colabserver.edu" \
    input.fasta output_folder/
```

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=chai
#SBATCH --gpus=1
#SBATCH --time=04:00:00
#SBATCH --mem=64G

module load cuda/12.1

mamba activate chailab

# Set custom download directory if needed
export CHAI_DOWNLOADS_DIR=/scratch/chai_models

# Run prediction with MSAs
chai-lab fold --use-msa-server --use-templates-server \
    my_complex.fasta \
    predictions/
```

## Python API Usage

You can also use Chai-1 programmatically:

```python
from chai_lab.chai1 import run_inference

# Run inference
results = run_inference(
    fasta_file="input.fasta",
    output_dir="output/",
    num_trunk_recycles=3,
    num_diffn_timesteps=200,
    seed=42
)
```

See `examples/predict_structure.py` in the repository for more details.

## Advanced Features

**Custom Templates**: Provide your own structure templates
**Experimental Restraints**: Specify inter-chain contacts or covalent bonds
**Custom MSAs**: Provide MSAs in `aligned.pqt` format

See the [restraints documentation](https://github.com/chaidiscovery/chai-lab/tree/main/examples/restraints) and [covalent bonds documentation](https://github.com/chaidiscovery/chai-lab/tree/main/examples/covalent_bonds) for advanced usage.

## Web Server

You can also test Chai-1 via the web interface: [https://lab.chaidiscovery.com](https://lab.chaidiscovery.com)

## Troubleshooting

**GPU compatibility**: Chai-1 requires bfloat16 support. Older GPUs may not work.

**Out of memory**: Use smaller complexes or request more GPU memory.

**Download location**: Control where models are downloaded:
```bash
export CHAI_DOWNLOADS_DIR=/path/to/storage
```

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
