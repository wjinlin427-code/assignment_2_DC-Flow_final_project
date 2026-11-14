# DC-Flow environment (Linux x86-64)

These files record the installed Conda and pip dependencies for Python 3.9.23 and PyTorch 2.8.0 with CUDA 12.8. Run from the repository root on Linux x86-64:

```bash
conda create -n DC-Flow --file envs/DC-Flow/conda-linux-64.explicit.txt
conda activate DC-Flow
python -m pip install --no-deps --index-url https://download.pytorch.org/whl/cu128 'torch==2.8.0+cu128'
grep -v '^torch==' envs/DC-Flow/requirements-pip.txt > "${TMPDIR:-/tmp}/dc-flow-requirements.txt"
python -m pip install -r "${TMPDIR:-/tmp}/dc-flow-requirements.txt"
python -m pip check
```

Alternatively, create the Conda base with `conda env create -f envs/DC-Flow/environment.yml` and then run the same activation and pip steps.

The [DC-Flow inference release](../../DCFlow) is included in this repository, with its [model checkpoint](../../DCFlow/checkpoints/dcflow.pt), [checksum manifest](../../DCFlow/checkpoints/manifest.json), and license notices. It is a snapshot of [the upstream inference release](https://github.com/mianqiu00/DCFlow/tree/db79a55d59bbf3b2133c794ba34287b901749a9d).

After activating the environment, install the included `dcflow` 1.0.0 package from the repository root:

```bash
python -m pip install --no-deps -e ./DCFlow
```

See the [inference README](../../DCFlow/README.md) for model usage and any external assets required by a workflow.

For workflows that invoke the included ProteinMPNN checkout, set these paths from the repository root:

```bash
export PROTEINMPNN_SOURCE="$PWD/ProteinMPNN"
export PROTEINMPNN_WEIGHTS="$PWD/ProteinMPNN/vanilla_model_weights/v_48_020.pt"
```

CUDA execution also requires a compatible NVIDIA driver.

The manifest records the source environment as found. Its dependency check reported missing dependencies declared by edgembar, fetkutils, ndfes, and packmol-memgen (matplotlib, PyYAML, netCDF4, RDKit, and pandas). Resolve those dependencies before running workflows that need them. A fresh installation has not been tested.
