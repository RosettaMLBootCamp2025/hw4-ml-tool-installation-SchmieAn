# DiffDock-PP

DiffDock-PP ([paper](https://arxiv.org/abs/2304.03889), [code](https://github.com/ketatam/DiffDock-PP)) is a graph neural network trained to complete de-noising of rigid transformations (rotation and translation) that parameterize the docking orientation between two rigid protein subunits.

DiffDock-PP is useful for predicting binding orientations between protein chains and can be used to orthogonally validate structure predictions, particularly for protein-protein docking.

The goal of this homework assignment is to install DiffDock-PP on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software. We'll be creating virtual environments using Conda/Mamba.

(If you don't have Mamba or prefer using Conda, just swap `mamba` for `conda` in the install commands below)

## Installation Steps

1. Clone the DiffDock-PP repository:
```bash
git clone https://github.com/ketatam/DiffDock-PP.git
cd DiffDock-PP
```

2. Create a new environment called "diffdock_pp":
```bash
mamba create -n diffdock_pp
```

3. Activate the new environment:
```bash
mamba activate diffdock_pp
```

4. Install PyTorch:
```bash
mamba install pytorch=1.13.0 pytorch-cuda=11.6 -c pytorch -c nvidia
```

5. Install PyG (PyTorch Geometric) packages:
```bash
mamba install pytorch-scatter pytorch-sparse pytorch-cluster pytorch-spline-conv pyg -c pyg
```

6. Install the remaining dependencies:
```bash
mamba install mkl=2024.0 "numpy<2.0" dill tqdm pyyaml pandas biopandas scikit-learn biopython e3nn wandb tensorboard tensorboardX matplotlib
```

## Testing the Installation

1. Create required directories:
```bash
mkdir storage
```

2. Run the test script on the DB5 benchmark:
```bash
bash DiffDock-PP/src/db5_inference.sh
```

If the command works without errors and the output folder `visualization/epoch-0/` contains PDB files of docked complexes, your installation was successful!

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=diffdock_pp
#SBATCH --gpus=1
#SBATCH --time=02:00:00
#SBATCH --mem=16G

module load cuda/11.6  # Adjust to match PyTorch CUDA version

mamba activate diffdock_pp

# Create output directory
mkdir -p storage

# Run inference
bash DiffDock-PP/src/db5_inference.sh
```

## HPC-Specific Notes

**CUDA Version**: DiffDock-PP was tested with PyTorch 1.13.0 and CUDA 11.6. Make sure your HPC has compatible CUDA modules:
```bash
module avail cuda
```

**GPU Requirements**: Docking predictions benefit from GPU acceleration. Request GPU resources when submitting jobs.

## Understanding the Output

The script generates:
- PDB files of predicted protein-protein complexes in `visualization/epoch-0/`
- Each PDB file represents a predicted docking pose
- Multiple poses may be generated for ensemble predictions

## Use Cases

- **Protein-Protein Docking**: Predict binding orientations between protein chains
- **Complex Validation**: Validate predicted protein-protein interfaces
- **Ensemble Generation**: Generate multiple docking poses to capture uncertainty

## Troubleshooting

**PyG installation failures**: Ensure CUDA toolkit is available and PyTorch is installed first.

**CUDA version mismatch**: Make sure the CUDA version for PyTorch matches your system's CUDA:
```bash
nvcc --version
```

**NumPy version**: The environment specifies `numpy<2.0` for compatibility. Don't upgrade NumPy beyond 2.0.

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
