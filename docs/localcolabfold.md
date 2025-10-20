# LocalColabFold

LocalColabFold ([code](https://github.com/YoshitakaMo/localcolabfold)) is a local installation of ColabFold, which provides an efficient implementation of AlphaFold2 protein structure prediction. ColabFold combines fast MSA generation from MMseqs2 with AlphaFold2's structure prediction capabilities, making it significantly faster than the original AlphaFold2 implementation.

LocalColabFold is useful for high-throughput protein structure prediction on your own HPC infrastructure without relying on Google Colab. It enables batch processing of structure predictions without time limits or GPU restrictions.

The goal of this homework assignment is to install LocalColabFold on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software.

**Important**: Make sure you have access to a GPU node for testing, as AlphaFold2 requires GPUs for efficient structure prediction.

## Installation Steps

1. Download the installation script:
```bash
wget https://raw.githubusercontent.com/YoshitakaMo/localcolabfold/main/install_colabbatch_linux.sh
```

2. Make the script executable and run it:
```bash
chmod +x install_colabbatch_linux.sh
./install_colabbatch_linux.sh
```

This will create a directory called `localcolabfold` with a conda environment called `colabfold_batch`.

The script will automatically:
- Install Miniconda (if not already present)
- Create a conda environment with ColabFold and all dependencies
- Download necessary model weights (~10-15 GB)

3. Add the environment to your PATH (add to your `~/.bashrc` for permanent access):
```bash
export PATH="/path/to/your/localcolabfold/colabfold-conda/bin:$PATH"
```

## Testing the Installation

1. Activate the ColabFold environment:
```bash
source localcolabfold/colabfold-conda/bin/activate
```

2. Create a test FASTA file with a small protein sequence:
```bash
echo ">test_protein
MKFLKFSLLTAVLLSVVFAFSSCGDDDDTYPYDVPDYAGTCGDDDDTYPYDVPDYA" > test.fasta
```

3. Run ColabFold on the test sequence:
```bash
colabfold_batch test.fasta test_output/
```

If the command completes successfully and creates PDB files in `test_output/`, your installation was successful!

## HPC Job Submission

For HPC clusters using SLURM, create a job script like this:

```bash
#!/bin/bash
#SBATCH --job-name=colabfold
#SBATCH --gpus=1
#SBATCH --time=04:00:00
#SBATCH --mem=32G

source /path/to/localcolabfold/colabfold-conda/bin/activate

colabfold_batch input.fasta output_dir/
```

## Troubleshooting

**Database Location**: By default, ColabFold downloads databases to `~/.cache/colabfold/`. On shared clusters, you may want to set `COLABFOLD_DOWNLOAD_DIR` to a shared location to avoid duplicate downloads:
```bash
export COLABFOLD_DOWNLOAD_DIR=/shared/path/colabfold_db
```

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
