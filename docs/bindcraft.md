# BindCraft

BindCraft ([paper](https://www.nature.com/articles/s41586-025-09429-6), [code](https://github.com/martinpacesa/BindCraft)) is an end-to-end binder design pipeline that combines AlphaFold2 backpropagation, ProteinMPNN, and PyRosetta to design protein binders against target proteins.

BindCraft is useful for de novo binder design, allowing you to select a target protein and automatically generate, optimize, and validate designed binders. It integrates multiple ML tools into a complete workflow, making it ideal for learning how protein design pipelines work in practice.

The goal of this homework assignment is to install BindCraft on your HPC cluster.

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software.

**Requirements**:
- CUDA-compatible Nvidia GPU (recommended: at least 32 GB GPU memory)
- Storage space: ~2 MB for code + ~5.3 GB for AlphaFold2 weights
- Conda or Mamba package manager

**Important**: BindCraft requires a PyRosetta license for commercial use. Academic use is typically covered by standard PyRosetta licensing.

## Installation Steps

1. Clone the BindCraft repository (replace `[install_folder]` with your desired path):
```bash
git clone https://github.com/martinpacesa/BindCraft [install_folder]
```

2. Navigate into the install folder:
```bash
cd [install_folder]
```

3. Run the installation script with your CUDA version:
```bash
bash install_bindcraft.sh --cuda '12.4' --pkg_manager 'conda'
```

**Notes**:
- Replace `12.4` with your cluster's CUDA version (check with `nvcc --version`)
- Use `--pkg_manager 'mamba'` if you prefer mamba (faster)
- If you leave `--cuda` blank, the installer will try to detect the version automatically (may not always work correctly)

The installation creates a conda environment called `BindCraft` with all dependencies.

## Testing the Installation

BindCraft comes with example settings. To test:

1. Activate the BindCraft environment:
```bash
conda activate BindCraft
```

2. Navigate to your BindCraft folder and run a test design:
```bash
cd /path/to/bindcraft/folder/
python -u ./bindcraft.py \
    --settings './settings_target/PDL1.json' \
    --filters './settings_filters/default_filters.json' \
    --advanced './settings_advanced/default_4stage_multimer.json'
```

This runs binder design against the PDL1 example target. If it starts generating trajectories without errors, your installation was successful!

**Note**: A complete binder design run can take hours to days depending on settings. For testing, you can stop it after a few trajectories complete.

## HPC Job Submission

For SLURM clusters, BindCraft provides a template job script:

```bash
sbatch ./bindcraft.slurm \
    --settings './settings_target/PDL1.json' \
    --filters './settings_filters/default_filters.json' \
    --advanced './settings_advanced/default_4stage_multimer.json'
```

Or create your own job script:

```bash
#!/bin/bash
#SBATCH --job-name=bindcraft
#SBATCH --gpus=1
#SBATCH --mem=64G
#SBATCH --time=24:00:00

conda activate BindCraft
cd /path/to/bindcraft/folder/

python -u ./bindcraft.py \
    --settings './settings_target/my_target.json' \
    --filters './settings_filters/default_filters.json' \
    --advanced './settings_advanced/default_4stage_multimer.json'
```

## Basic Usage Workflow

1. **Prepare your target**: Place your target protein PDB file in the BindCraft folder

2. **Configure target settings**: Edit or create a JSON file in `settings_target/`:
```json
{
    "design_path": "./my_binder_designs",
    "binder_name": "my_binder",
    "starting_pdb": "./my_target.pdb",
    "chains": "A",
    "target_hotspot_residues": "A10-20",
    "lengths": "50-100",
    "number_of_final_designs": 100
}
```

3. **Run the pipeline**: Submit the job to design binders

4. **Analyze results**: BindCraft generates statistics and filtered designs in the output folder

## Key Settings Explained

**Target Settings** (`settings_target/*.json`):
- `starting_pdb`: Your target protein structure
- `chains`: Which chains to target
- `target_hotspot_residues`: Specific residues to target (or `null` for automatic selection)
- `lengths`: Range of binder lengths to design (e.g., `50-100`)
- `number_of_final_designs`: How many designs passing filters to generate before stopping

**Filters** (`settings_filters/*.json`):
- Control which designs to keep based on confidence scores (pLDDT, pTM, i_pTM)
- Interface quality metrics (shape complementarity, energy, etc.)
- Default filters are usually a good starting point

**Advanced Settings** (`settings_advanced/*.json`):
- Design algorithm (default: `4stage`)
- Number of iterations for each design stage
- AF2 and MPNN parameters
- Design weights for various objectives

## Tips for Success

1. **Trim your target PDB**: Remove unnecessary chains/residues to reduce memory requirements and speed up design

2. **Start with defaults**: Use the default filter and advanced settings initially, then adjust based on results

3. **Generate enough designs**: Aim for at least 100 final designs passing filters (the script recommends ordering top 5-20 for experiments)

4. **Be patient**: Expect to generate hundreds to thousands of trajectories to get enough accepted binders, especially for difficult targets

5. **Monitor acceptance rate**: If very few trajectories pass filters, you may need to adjust design weights or filters

## Understanding BindCraft's Integration

BindCraft demonstrates a complete protein design workflow:
1. **Design**: Uses AlphaFold2 backpropagation to generate binder backbones
2. **Optimize**: Uses ProteinMPNN to design sequences for the backbones
3. **Validate**: Uses AlphaFold2 to predict the designed complex
4. **Score**: Uses PyRosetta to evaluate interface quality

This integration showcases how real-world protein design combines multiple tools into a pipeline.

## Troubleshooting

**GPU memory errors**: Reduce the size of your target PDB or request more GPU memory. BindCraft recommends at least 32 GB GPU memory for larger complexes.

**CUDA version mismatch**: Make sure you specified the correct CUDA version during installation. Check with `nvcc --version`.

**Low acceptance rate**: If very few designs pass filters, you may need to:
- Adjust design weights in advanced settings
- Relax filter thresholds
- Change target hotspot selection
- Increase the target site area

**Wiki for detailed help**: Before posting issues, check the complete wiki at https://github.com/martinpacesa/BindCraft/wiki/De-novo-binder-design-with-BindCraft

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
