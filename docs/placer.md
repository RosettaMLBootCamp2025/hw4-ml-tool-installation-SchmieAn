# PLACER

PLACER ([paper](https://www.biorxiv.org/content/10.1101/2024.09.25.614868v1), [code](https://github.com/baker-laboratory/PLACER)) stands for **P**rotein-**L**igand **A**tomistic **C**onformational **E**nsemble **R**esolver. It's a graph neural network that operates entirely at the atomic level to generate conformational ensembles of protein-ligand complexes.

PLACER is useful for:
- Protein-ligand docking with conformational sampling
- Side chain prediction and refinement
- Modeling conformational heterogeneity through ensemble generation
- Predicting binding poses with uncertainty quantification

The goal of this homework assignment is to install PLACER on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software. We'll be creating virtual environments using Conda/Mamba.

## Installation Steps

1. Clone the repository:
```bash
git clone https://github.com/baker-laboratory/PLACER.git
cd PLACER
```

The repository contains model weights and is ready to run after environment setup.

2. Create the conda environment from the provided file:
```bash
mamba env create -f envs/placer_env.yml
```

3. Activate the environment:
```bash
mamba activate placer_env
```

## Testing the Installation

Run a simple heme docking prediction:

```bash
python run_PLACER.py \
    --ifile examples/inputs/dnHEM1.pdb \
    --odir test_output \
    --rerank prmsd \
    -n 10 \
    --ligand_file HEM:examples/ligands/HEM.mol2
```

If this completes successfully and creates output files in `test_output/`, your installation was successful!

## Command Line Usage

Basic syntax:
```bash
python run_PLACER.py -f INPUT.pdb -o OUTPUT_DIR -n NUM_SAMPLES
```

Key parameters:
- `-f` or `--ifile`: Input PDB/mmCIF file
- `-o` or `--odir`: Output directory
- `-n` or `--nsamples`: Number of ensemble samples (50-100 recommended for docking)
- `--rerank`: Rank outputs by confidence metric (prmsd, plddt, or plddt_pde)
- `--predict_ligand`: Specify which ligand(s) to predict
- `--fixed_ligand`: Keep certain ligands fixed in place
- `--ligand_file`: Provide SDF/MOL2 files for correct atom typing

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=placer
#SBATCH --gpus=1
#SBATCH --time=04:00:00
#SBATCH --mem=32G

module load cuda/12.1

mamba activate placer_env

# Predict ligand binding with 100 samples
python run_PLACER.py \
    --ifile my_complex.pdb \
    --odir results/ \
    --predict_ligand LIG-501 \
    --rerank prmsd \
    -n 100 \
    --ligand_file LIG:ligand.sdf
```

## Understanding Confidence Scores

PLACER provides several confidence metrics:

- **prmsd**: Predicted RMSD (recommended for docking). Good: <2.0, Acceptable: <4.0
- **plddt**: Predicted lDDT from 1D track. Good: >0.8
- **plddt_pde**: Predicted lDDT from 2D track. Good: >0.8
- **fape**: All-atom FAPE loss
- **rmsd**: Actual RMSD to reference (if available)
- **kabsch**: Superimposed RMSD (conformation accuracy)

## Example Use Cases

**Ligand docking with cofactor fixed**:
```bash
python run_PLACER.py \
    --ifile 4dtz.cif \
    --odir output/ \
    --predict_ligand D-LDP-501 \
    --fixed_ligand C-HEM-500 \
    -n 100 \
    --rerank prmsd
```

**Side chain prediction**:
```bash
python run_PLACER.py \
    --ifile protein.pdb \
    --odir output/ \
    --target_res A-149 \
    -n 50 \
    --no-use_sm  # apo mode, no small molecule
```

**Multiple ligands simultaneously**:
```bash
python run_PLACER.py \
    --ifile complex.pdb \
    --odir output/ \
    --predict_multi \
    --predict_ligand LIG1 LIG2 \
    -n 100
```

## Python API

PLACER can also be imported as a Python module:

```python
import sys
sys.path.append("/path/to/PLACER")
import PLACER

placer = PLACER.PLACER()  # Load model

pl_input = PLACER.PLACERinput()
pl_input.pdb("complex.pdb")
pl_input.name("my_prediction")
pl_input.ligand_reference({"HEM": "heme.mol2"})

# Run 50 predictions
outputs = placer.run(pl_input, 50)
```

## Performance Notes

**GPU**: 1-3 seconds per model (typical ligand-protein complex)
**CPU (1 core)**: ~7 minutes per model
**CPU (8 cores)**: ~1 minute per model

Ligands with many symmetric groups take longer.

## Troubleshooting

**Non-planar aromatic rings**: Provide SDF/MOL2 file with `--ligand_file` for correct bonding information.

**Missing ligands in PDB**: Currently, ligands must be in the input PDB. SMILES-only input not supported.

**Custom residues**: Use `--residue_json` to define non-canonical amino acids.

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
