# ESMFold

ESMFold ([paper](https://www.science.org/doi/abs/10.1126/science.ade2574), [code](https://github.com/facebookresearch/esm)) is an end-to-end single sequence structure predictor that uses the ESM-2 language model to generate accurate 3D protein structures directly from sequence.

ESMFold is useful for fast, accurate protein structure prediction without requiring MSAs (though MSAs can be used to improve performance). It's significantly faster than AlphaFold2 while maintaining competitive accuracy.

The goal of this homework assignment is to install ESMFold on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software. We'll be creating virtual environments using Conda/Mamba.

**Requirements**:
- Python ≤ 3.9
- PyTorch installed
- `nvcc` available (for compiling OpenFold dependencies)
- CUDA-capable GPU recommended

## Installation Steps

1. Create a conda environment with Python 3.9:
```bash
mamba create -n esmfold python=3.9
mamba activate esmfold
```

2. Install PyTorch (adjust CUDA version as needed):
```bash
mamba install pytorch pytorch-cuda=12.1 -c pytorch -c nvidia
```

3. Install ESM with ESMFold dependencies:
```bash
pip install "fair-esm[esmfold]"
```

4. Install remaining OpenFold dependencies:
```bash
pip install 'dllogger @ git+https://github.com/NVIDIA/dllogger.git'
pip install 'openfold @ git+https://github.com/aqlaboratory/openfold.git@4b41059694619831a7db195b7e0988fc4ff3a307'
```

**Note**: If OpenFold installation fails, verify that `nvcc` is available and a CUDA-compatible PyTorch is installed.

## Alternative: Use Conda Environment File

Alternatively, you can use a pre-configured environment:
```bash
wget https://raw.githubusercontent.com/facebookresearch/esm/main/environment.yml
mamba env create -f environment.yml
mamba activate esmfold
```

## Testing the Installation

Create a test script `test_esmfold.py`:

```python
import torch
import esm

# Load ESMFold model
model = esm.pretrained.esmfold_v1()
model = model.eval().cuda()  # Remove .cuda() if using CPU

# Test sequence
sequence = "MKTVRQERLKSIVRILERSKEPVSGAQLAEELSVSRQVIVQDIAYLRSLGYNIVATPRGYVLAGG"

# Run prediction
with torch.no_grad():
    output = model.infer_pdb(sequence)

# Save output
with open("test_result.pdb", "w") as f:
    f.write(output)

print("Structure prediction successful! Output saved to test_result.pdb")
```

Run the test:
```bash
python test_esmfold.py
```

If this creates `test_result.pdb`, your installation was successful!

## Command Line Interface

ESM also provides a command line tool for batch prediction:

```bash
esm-fold -i sequences.fasta -o output_pdbs/
```

Additional options:
- `--num-recycles`: Number of recycles (default: 4)
- `--max-tokens-per-batch`: Batch shorter sequences together
- `--chunk-size`: Reduce memory usage (values: 128, 64, 32)
- `--cpu-only`: Run on CPU only
- `--cpu-offload`: Offload to CPU RAM for longer sequences

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=esmfold
#SBATCH --gpus=1
#SBATCH --time=04:00:00
#SBATCH --mem=32G

module load cuda/12.1

mamba activate esmfold

# Predict structures for all sequences in FASTA file
esm-fold -i my_proteins.fasta -o predictions/ \
    --num-recycles 4 \
    --max-tokens-per-batch 1024
```

## Troubleshooting

**OpenFold installation fails**: Double-check that `nvcc` is available:
```bash
nvcc --version
```
Load CUDA modules if needed:
```bash
module load cuda
```

**Out of memory errors**: Use `--chunk-size` to reduce memory:
```bash
esm-fold -i input.fasta -o output/ --chunk-size 64
```

**For very long sequences**: Use CPU offloading:
```bash
esm-fold -i input.fasta -o output/ --cpu-offload
```

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
