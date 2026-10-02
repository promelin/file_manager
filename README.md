# File Manager

`file-manager` is a Codex plugin that keeps model-development datasets reproducible and auditable. It creates separate raw-download and processed-data workflows, with scripts and detailed logs for one or many datasets.

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
- `Create the data workflow, but do not download or process anything yet.`
