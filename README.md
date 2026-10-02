# File Manager

`file-manager` is a Codex plugin that normalizes dataset files under a user-specified `data` directory. When the user asks to download, process, or analyze a dataset, the plugin keeps raw files, processed files, scripts, logs, and analysis results in a predictable single- or multi-dataset layout instead of scattering them across the working directory.

Requested analyses live beside `raw/` and `processed/` under `data_analysis/<user-chosen-name>/`. Each analysis directory contains only `analysis.py` and `results.txt` unless the user explicitly asks for additional outputs.

## Install in Codex

This repository is private, so authenticate GitHub access before installing it.

Add the repository as a Codex plugin marketplace:

```bash
codex plugin marketplace add promelin/file_manager
codex plugin add file-manager@file-manager
```

Alternatively, after adding the marketplace, restart the ChatGPT desktop app, open the Plugins Directory, select **File Manager**, and install the plugin there.

## Use

Describe a model-development task naturally or invoke the bundled skill explicitly as `$file-manager`.

Examples:

- `Use $file-manager to download and preprocess CIFAR-10 for this training project.`
- `Organize MNIST and Fashion-MNIST as separate datasets for model evaluation.`
- `Analyze solvent kinds in the managed dataset and use solvent_kinds as the analysis directory.`
- `Create the data workflow, but do not download or process anything yet.`
