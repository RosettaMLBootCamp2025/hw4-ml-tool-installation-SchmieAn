# LigandMPNN

LigandMPNN ([paper](https://www.biorxiv.org/content/10.1101/2023.12.22.573103v1), [code](https://github.com/dauparas/LigandMPNN)) is a deep learning model for context-aware protein sequence design. It extends ProteinMPNN to handle small molecules, metal ions, and other non-protein components in protein design tasks.

LigandMPNN is useful for designing protein sequences that interact with ligands, cofactors, or other small molecules. It can also be used for side chain packing and evaluating sequence-structure compatibility.

The goal of this homework assignment is to install LigandMPNN on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software. We'll be creating virtual environments using Conda/Mamba.

(If you don't have Mamba or prefer using Conda, just swap `mamba` for `conda` in the install commands below)

## Installation Steps

1. Clone the LigandMPNN repository:
```bash
git clone https://github.com/dauparas/LigandMPNN.git
cd LigandMPNN
```

2. Download the model parameters:
```bash
bash get_model_params.sh "./model_params"
```

3. Create a new conda environment:
```bash
mamba create -n ligandmpnn_env python=3.11
```

4. Activate the environment:
```bash
mamba activate ligandmpnn_env
```

5. Install dependencies:
```bash
pip3 install -r requirements.txt
```

The requirements include PyTorch, NumPy, and ProDy for reading/writing PDB files.

## Testing the Installation

Run a test design on the provided example structure:

```bash
python run.py \
    --seed 111 \
    --pdb_path "./inputs/1BC8.pdb" \
    --out_folder "./outputs/test_output"
```

If this completes successfully and generates output files in `./outputs/test_output/`, your installation was successful!

## HPC-Specific Notes

**GPU Requirements**: LigandMPNN can run on both CPU and GPU. GPU is recommended for faster processing. Make sure to request GPU resources when submitting jobs on your cluster.

**Batch Processing**: For designing multiple structures, use the `--pdb_path_multi` option with a JSON file listing all input PDBs. This is more efficient since the model is loaded only once.

## Example HPC Job Script

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=ligandmpnn
#SBATCH --gpus=1
#SBATCH --time=02:00:00
#SBATCH --mem=16G

module load cuda/12.1  # Adjust to your cluster

mamba activate ligandmpnn_env

python run.py \
    --model_type "ligand_mpnn" \
    --seed 111 \
    --pdb_path "./inputs/my_protein.pdb" \
    --out_folder "./outputs/my_design" \
    --number_of_batches 5
```

## Common Use Cases

**Design with ligand context**:
```bash
python run.py \
    --model_type "ligand_mpnn" \
    --pdb_path "./input.pdb" \
    --ligand "HEM" \
    --out_folder "./output"
```

**Fix specific residues**:
```bash
python run.py \
    --pdb_path "./input.pdb" \
    --fixed_residues "A10 A20 A30" \
    --out_folder "./output"
```

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
