# RFdiffusion2

RFdiffusion2 ([paper](https://www.biorxiv.org/content/10.1101/2025.04.10.488300v1), [code](https://github.com/RosettaCommons/RFdiffusion2)) is a protein design model capable of atom-level active site scaffolding. It extends the original RFdiffusion to enable more precise control over protein-ligand interactions at the atomic level.

RFdiffusion2 is useful for designing proteins with specific active site geometries, enzyme design with atomic-level motif constraints, and designing binders to small molecules with precise interaction patterns.

The goal of this homework assignment is to install RFdiffusion2 on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software.

**IMPORTANT**: RFdiffusion2 uses Apptainer/Singularity containers. Most academic HPCs support Singularity/Apptainer, but verify with your system administrator if unsure.

##Installation Steps

1. Clone the repository:
```bash
git clone https://github.com/RosettaCommons/RFdiffusion2.git
cd RFdiffusion2
```

2. Add the repo to your PYTHONPATH (add this to your `~/.bashrc`):
```bash
export PYTHONPATH="/path/to/your/RFdiffusion2:$PYTHONPATH"
```

3. Download the model weights and containers:
```bash
python setup.py
```

**Note**: This downloads large files (~several GB) and can take 30+ minutes. If the download is interrupted, run `python setup.py overwrite` to resume.

4. Install Apptainer/Singularity

**For Apptainer on Ubuntu/Debian**:
```bash
sudo add-apt-repository -y ppa:apptainer/ppa
sudo apt update
sudo apt install -y apptainer
```

**For HPC clusters**: Apptainer/Singularity is usually pre-installed. Load the module:
```bash
module load apptainer
# or
module load singularity
```

## Docker Alternative for HPC

**IMPORTANT HPC NOTE**: Most academic HPCs do NOT support Docker for security reasons. If the official documentation mentions Docker, use Singularity/Apptainer instead. The downloaded `.sif` file in `rf_diffusion/exec/` is a Singularity/Apptainer container that works on HPCs.

## Testing the Installation

Run a demo case:

```bash
apptainer exec --nv rf_diffusion/exec/bakerlab_rf_diffusion_aa.sif \
    rf_diffusion/benchmark/pipeline.py \
    --config-name=open_source_demo \
    sweep.benchmarks=active_site_unindexed_atomic_partial_ligand
```

**Note**: Omit the `--nv` flag if running without GPU.

This will generate a protein design with an atomized active site motif. The output will be in:
```
pipeline_outputs/<timestamp>_open_source_demo
```

## HPC-Specific Notes

**GPU Requirements**: RFdiffusion2 requires CUDA-capable GPUs for reasonable performance. Request GPU nodes when submitting jobs.

**Container Execution**: Use `apptainer` (or `singularity`) instead of `docker`:
```bash
# NOT: docker run ...
# INSTEAD:
apptainer exec --nv <container.sif> <command>
```

## Example HPC Job Script

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=rfd2
#SBATCH --gpus=1
#SBATCH --time=04:00:00
#SBATCH --mem=32G

module load apptainer
module load cuda/12.1

cd /path/to/RFdiffusion2

apptainer exec --nv rf_diffusion/exec/bakerlab_rf_diffusion_aa.sif \
    rf_diffusion/benchmark/pipeline.py \
    --config-name=open_source_demo
```

## Troubleshooting

**No GPU available**: The demo will be extremely slow on CPU (30+ minutes per case). Always use GPUs for production runs.

**Container permission errors**: Ensure you have read/execute permissions on the `.sif` file.

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
