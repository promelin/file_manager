---
name: file-manager
description: Organize raw downloads and processed datasets for model-development work. Use when a task involves acquiring, preparing, transforming, training with, fine-tuning on, or evaluating against one or more datasets.
---

# Model Data File Manager

For model-development work, keep dataset acquisition and post-processing reproducible and auditable under the task's top-level working directory.

## Choose the layout

Treat the project or task root selected by the user as the top-level working directory. Create `data/` directly under that directory.

Use this layout when the task has one dataset:

```text
data/
├── raw/
│   ├── download.py
│   ├── download.log
│   └── <downloaded source files>
└── processed/
    ├── process.py
    ├── process.log
    └── <processed output files>
```

Use this layout when the task has multiple distinct datasets:

```text
data/
├── <dataset-name>/
│   ├── raw/
│   │   ├── download.py
│   │   ├── download.log
│   │   └── <downloaded source files>
│   └── processed/
│       ├── process.py
│       ├── process.log
│       └── <processed output files>
└── <another-dataset-name>/
    ├── raw/
    └── processed/
```

Use short, stable, filesystem-safe dataset directory names. Do not add a dataset-name level for a single dataset unless the user requests it or the existing project already follows that convention.

## Download raw data

Place each dataset's download implementation in its `raw/download.py`.

- Make the download reproducible and safe to rerun.
- Save original source files only inside the corresponding `raw/` directory.
- Preserve downloaded source files unchanged; do not overwrite or transform them during post-processing.
- Do not hard-code passwords, API keys, access tokens, or other credentials. Use the environment or the user's existing authenticated tooling.
- Write download progress and outcomes to `raw/download.log`, including timestamps, sources, destinations, completion status, and actionable error details. Record file sizes or checksums when they materially help verify integrity.
- If downloading requires credentials, acceptance of terms, substantial cost, or another user-authorized external action, stop at the relevant boundary and obtain the required authorization.

## Process data

Place each dataset's post-processing implementation in its `processed/process.py`.

- Implement the transformations required by the user's model-development task rather than imposing a generic pipeline.
- Read source data from the matching `raw/` directory and write generated data only to the matching `processed/` directory.
- Keep processing reproducible and safe to rerun. Use explicit parameters or a deterministic seed when randomness is required.
- Write a detailed execution record to `processed/process.log`, including timestamps, input paths, configured transformations, relevant counts or shapes, output paths, completion status, and actionable error details.
- Keep intermediate and final processed artifacts in `processed/`; do not modify the original raw files.

## Work with existing projects

Inspect the current tree before creating files. Preserve compatible existing data, scripts, logs, and project conventions. Add missing pieces or make the smallest necessary update instead of deleting or silently replacing user files.

When the user asks to execute the workflow, run the relevant scripts and verify their outputs and logs. When the request only calls for scaffolding or planning, create the required structure and scripts without initiating downloads or expensive processing.

In the final response, summarize the chosen single- or multi-dataset layout, the created or updated scripts, the generated data artifacts, and the locations of both logs. Clearly report any download or processing step that was not run.
