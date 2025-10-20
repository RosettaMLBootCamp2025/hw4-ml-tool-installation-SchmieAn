# RFdiffusion All Atom (OPTIONAL)

RFdiffusion All Atom ([code](https://github.com/baker-laboratory/rf_diffusion_all_atom)) is the predecessor to RFdiffusion2 and enables all-atom protein design with small molecule binding. It can design protein binders to ligands with all-atom precision.

**Note**: This tool is marked as OPTIONAL because RFdiffusion2 is the newer, more capable version. Install this only if you're interested in exploring the earlier methodology or if specific features are needed.

RFdiffusion All Atom is useful for designing small molecule binders and incorporating protein motifs in the design process.

The goal of this homework assignment is to install RFdiffusion All Atom on your HPC cluster (optional).

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software.

**IMPORTANT**: Like RFdiffusion2, this uses Apptainer/Singularity containers, not Docker (which most academic HPCs don't support).

## Installation Steps

1. Clone the repository:
```bash
git clone https://github.com/baker-laboratory/rf_diffusion_all_atom.git
cd rf_diffusion_all_atom
```

2. Download the Singularity container:
```bash
wget http://files.ipd.uw.edu/pub/RF-All-Atom/containers/rf_se3_diffusion.sif
```

3. Download the model weights:
```bash
wget http://files.ipd.uw.edu/pub/RF-All-Atom/weights/RFDiffusionAA_paper_weights.pt
```

4. Initialize git submodules:
```bash
git submodule init
git submodule update
```

5. Install Apptainer (if not already available):

**For HPC clusters**:
```bash
module load apptainer
# or
module load singularity
```

## Docker vs Singularity/Apptainer for HPC

**IMPORTANT**: The official documentation uses Docker commands, but most academic HPCs do NOT support Docker. Replace Docker commands with Apptainer/Singularity:

```bash
# ORIGINAL (Docker - won't work on most HPCs):
# docker run --gpus all -v $(pwd):/workspace rf_se3_diffusion.sif ...

# CORRECTED (Apptainer - works on HPCs):
apptainer run --nv rf_se3_diffusion.sif ...
```

## Testing the Installation

Run a ligand binder design example:

```bash
apptainer run --nv rf_se3_diffusion.sif -u run_inference.py \
    inference.deterministic=True \
    diffuser.T=100 \
    inference.output_prefix=output/ligand_test/sample \
    inference.input_pdb=input/7v11.pdb \
    contigmap.contigs="['150-150']" \
    inference.ligand=OQO \
    inference.num_designs=1 \
    inference.design_startnum=0
```

**Note**: Omit `--nv` flag if running without GPU.

Expected outputs:
- `output/ligand_test/sample_0.pdb` - The design PDB
- `output/ligand_test/sample_0_Xt-1_traj.pdb` - Denoising trajectory
- `output/ligand_test/sample_0_X0-1_traj.pdb` - Predicted ground truth at each step

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=rfdaa
#SBATCH --gpus=1
#SBATCH --time=02:00:00
#SBATCH --mem=16G

module load apptainer
module load cuda/12.1

cd /path/to/rf_diffusion_all_atom

apptainer run --nv rf_se3_diffusion.sif -u run_inference.py \
    inference.deterministic=True \
    diffuser.T=100 \
    inference.output_prefix=output/my_design/sample \
    inference.input_pdb=input/my_protein.pdb \
    contigmap.contigs="['150-150']" \
    inference.ligand=HEM \
    inference.num_designs=10
```

## Troubleshooting

**Container not found**: Ensure the `.sif` file is in the current directory or provide the full path.

**GPU errors**: Make sure CUDA modules are loaded and GPU is available in your allocation.

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
