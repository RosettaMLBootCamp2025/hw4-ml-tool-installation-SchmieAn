# ESM3 (OPTIONAL)

ESM3 ([paper](https://www.science.org/doi/10.1126/science.ads0018), [code](https://github.com/evolutionaryscale/esm)) is a frontier generative model for biology that can jointly reason across three fundamental biological properties of proteins: sequence, structure, and function. It represents a multimodal generative masked language model.

**Note**: This tool is marked as OPTIONAL as it's primarily a research/generative model. Install if you're interested in protein generation and design beyond structure prediction.

ESM3 is useful for protein generation, function prediction, and multimodal protein design tasks. It can generate novel proteins with desired properties.

The goal of this homework assignment is to install ESM3 on your HPC cluster (optional).

## Preparation Work

This assignment assumes that you have access to a computing cluster that allows installation of new software. We'll be creating virtual environments using Conda/Mamba.

## Installation Steps

1. Create a conda environment:
```bash
mamba create -n esm3 python=3.10
mamba activate esm3
```

2. Install the ESM library:
```bash
pip install esm
```

## HuggingFace Authentication

ESM3 weights are stored on HuggingFace Hub. You'll need to authenticate:

1. Create a HuggingFace account at [huggingface.co](https://huggingface.co)
2. Generate an API token with "Read" permission
3. Authenticate:

```python
from huggingface_hub import login
login()  # Follow prompts to enter your token
```

## Testing the Installation

Create a test script `test_esm3.py`:

```python
from huggingface_hub import login
from esm.models.esm3 import ESM3
from esm.sdk.api import ESMProtein, GenerationConfig

# Authenticate (first time only)
login()

# Load the model (this downloads weights on first run)
model = ESM3.from_pretrained("esm3-small-2024-08").to("cuda")  # or "cpu"

# Generate a protein sequence completion
prompt = "MKTVRQ_______________QLAEELSVSRQVIVQDIAYLRSLG"
protein = ESMProtein(sequence=prompt)

# Generate sequence
protein = model.generate(
    protein,
    GenerationConfig(track="sequence", num_steps=8, temperature=0.7)
)

print("Generated sequence:")
print(protein.sequence)

# Generate structure
protein = model.generate(
    protein,
    GenerationConfig(track="structure", num_steps=8)
)

# Save structure
protein.to_pdb("./generated.pdb")
print("Structure saved to generated.pdb")
```

Run the test:
```bash
python test_esm3.py
```

## Available ESM3 Models

- `esm3-small-2024-08` (1.4B parameters) - Fastest, runs locally
- `esm3-medium-2024-08` (7B parameters) - Via Forge API
- `esm3-large-2024-03` (98B parameters) - Via Forge API

## ESM C for Embeddings

If you only need protein embeddings (not generation), use ESM C instead:

```python
from esm.models.esmc import ESMC
from esm.sdk.api import ESMProtein, LogitsConfig

protein = ESMProtein(sequence="MKTVRQERLK")
client = ESMC.from_pretrained("esmc_300m").to("cuda")

# Get embeddings
protein_tensor = client.encode(protein)
logits_output = client.logits(
    protein_tensor,
    LogitsConfig(sequence=True, return_embeddings=True)
)

print(logits_output.embeddings)
```

## HPC Job Script Example

For SLURM clusters:

```bash
#!/bin/bash
#SBATCH --job-name=esm3
#SBATCH --gpus=1
#SBATCH --time=02:00:00
#SBATCH --mem=32G

module load cuda/12.1

mamba activate esm3

python generate_protein.py
```

## Use Cases

- **Protein generation**: Create novel proteins with desired properties
- **Sequence completion**: Fill in missing regions
- **Structure prediction**: Generate 3D structures from sequence
- **Function prediction**: Predict protein function from sequence/structure
- **Protein embeddings**: Extract representations (use ESM C)

## Troubleshooting

**Authentication errors**: Ensure you've created a HuggingFace token and run `login()`.

**Download issues**: Model weights are large. Ensure adequate storage and network.

**GPU memory**: The small model (1.4B) requires significant GPU memory. Use CPU if needed.

If you need help, contact Ian Anderson at icanderson@ucdavis.edu.
