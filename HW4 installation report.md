# HW4 installation report
Student name: Anne Schmieder
HPC Cluster: University of Leipzig HPC Cluster

##successfully installed tools

### LigandMPNN
- Installation Date: 2025-10-15
- Environment name: ligandmpnn
- test command used: python run.py --seed 111 --pdb_path "./inputs/1BC8.pdb" --out_folder "./outputs/test_output"
- test result: 
Designing protein from this path: ./inputs/1BC8.pdb
These residues will be redesigned:  ['C1', 'C2', 'C3', 'C4', 'C5', 'C6', 'C7', 'C8', 'C9', 'C10', 'C11', 'C12', 'C13', 'C14', 'C15', 'C16', 'C17', 'C18', 'C19', 'C20', 'C21', 'C22', 'C23', 'C24', 'C25', 'C26', 'C27', 'C28', 'C29', 'C30', 'C31', 'C32', 'C33', 'C34', 'C35', 'C36', 'C37', 'C38', 'C39', 'C40', 'C41', 'C42', 'C43', 'C44', 'C45', 'C46', 'C47', 'C48', 'C49', 'C50', 'C51', 'C52', 'C53', 'C54', 'C55', 'C56', 'C57', 'C58', 'C59', 'C60', 'C61', 'C62', 'C63', 'C64', 'C65', 'C66', 'C67', 'C68', 'C69', 'C70', 'C71', 'C72', 'C73', 'C74', 'C75', 'C76', 'C77', 'C78', 'C79', 'C80', 'C81', 'C82', 'C83', 'C84', 'C85', 'C86', 'C87', 'C88', 'C89', 'C90', 'C91', 'C92', 'C93']
These residues will be fixed:  []

### RFdiffusion-AA
- Installation Date: 2025-10-16
- Environment name: rfdiffusion_aa
- test command used: bash example_OQO.sh
- test result: output/ligand_only has files in it

### ESMFold
- Installation Date: 2025-10-23
- Environment name: esmfold
- test command used: python test_esmfold.py 
- test result: Notify Event error (ImportError: /home/sc.uni-leipzig.de/ah57zoxo/anaconda3/envs/esmfold/lib/python3.7/site-packages/torch/lib/libtorch_cpu.so: undefined symbol: iJIT_NotifyEvent)

BUT

###ESMfold from a collegue successfully installed in a different workspace

### ChaiLab
- Installation Date: 2025-10-24
- Environment name: chailab
- test command used: chai-lab fold test.fasta output_folder/
- test result: files in the output_folder folder


##Installation challenges

### RFdiffusion2
- challenge: no access apptainer, singularity or docker on the HPC Cluster --> not solved yet
- Note: already installes RFdiffusion-aa on the Cluster and RFdiffusion locally, which both work, but I could not test them with the provided files

### OpenFold
-challange: installation proess was all successfull but when trying the test command:

python run_pretrained_openfold.py --fasta_paths /work/ah57zoxo-ProteinGeneration/openfold/rscb_pdb_1UBQ.fasta --output_dir /work/ah57zoxo-ProteinGeneration/results/

always some kind of error occurred, in the end it was:

/home/sc.uni-leipzig.de/ah57zoxo/anaconda3/envs/openfold-env/lib/python3.10/site-packages/deepspeed/runtime/zero/linear.py:67: FutureWarning: `torch.cuda.amp.custom_bwd(args...)` is deprecated. Please use `torch.amp.custom_bwd(args..., device_type='cuda')` instead.
  def backward(ctx, grad_output):
Traceback (most recent call last):
  File "/work/ah57zoxo-ProteinGeneration/openfold/run_pretrained_openfold.py", line 46, in <module>
    from openfold.utils.script_utils import (load_models_from_command_line, parse_fasta, run_model,
  File "/work/ah57zoxo-ProteinGeneration/openfold/openfold/utils/script_utils.py", line 10, in <module>
    from openfold.model.model import AlphaFold
  File "/work/ah57zoxo-ProteinGeneration/openfold/openfold/model/model.py", line 29, in <module>
    from openfold.model.embedders import (
  File "/work/ah57zoxo-ProteinGeneration/openfold/openfold/model/embedders.py", line 29, in <module>
    from openfold.model.primitives import Linear, LayerNorm
  File "/work/ah57zoxo-ProteinGeneration/openfold/openfold/model/primitives.py", line 30, in <module>
    from flash_attn.bert_padding import unpad_input
  File "/home/sc.uni-leipzig.de/ah57zoxo/anaconda3/envs/openfold-env/lib/python3.10/site-packages/flash_attn/__init__.py", line 3, in <module>
    from flash_attn.flash_attn_interface import (
  File "/home/sc.uni-leipzig.de/ah57zoxo/anaconda3/envs/openfold-env/lib/python3.10/site-packages/flash_attn/flash_attn_interface.py", line 15, in <module>
    import flash_attn_2_cuda as flash_attn_gpu
ImportError: /home/sc.uni-leipzig.de/ah57zoxo/anaconda3/envs/openfold-env/lib/python3.10/site-packages/flash_attn_2_cuda.cpython-310-x86_64-linux-gnu.so: undefined symbol: _ZN3c105ErrorC2ENS_14SourceLocationESs

