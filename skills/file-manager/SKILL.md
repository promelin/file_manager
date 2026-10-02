---
name: file-manager
description: Standardize dataset downloads, post-processing, and scoped analysis inside a user-specified data directory. Use when the user identifies a work or data directory and asks to download datasets, process managed datasets, or analyze them under a user-chosen analysis subdirectory; do not trigger for model training alone.
---

# Model Data File Manager

Keep dataset downloads, processing code, analysis code, logs, and results inside one normalized data tree so model-development tasks do not scatter files across the working directory.

## Trigger boundary

Use this skill when any condition is met:

- The user identifies a working directory or a `data` directory and asks to download one or more named datasets there.
- The user asks to continue by processing a dataset already organized under that same data directory.
- The user asks to analyze a managed dataset; the analysis subdirectory must be chosen by the user, for example `solvent_kinds`.

Do not trigger merely because a task mentions model training, fine-tuning, inference, or evaluation. Do not use it for unrelated file organization. If the skill is invoked explicitly but the target working or data directory is ambiguous, ask the user to identify it before creating, downloading, processing, or analyzing files.

## Resolve the data root

- If the user specifies a directory named `data`, use that directory exactly as the data root.
- If the user specifies a project or working directory, create or use its direct child named `data`.
- Keep downloaded data, processing artifacts, analysis artifacts, dataset-specific scripts, and their records inside this data root. Do not leave generated dataset files elsewhere in the working directory.
- Resolve the path before downloading, processing, or analyzing anything, and report the resolved data root.

## Choose the layout

Use this layout when the data root contains one dataset:

```text
data/
├── raw/
│   ├── download.py
│   ├── download.log
│   └── <downloaded source files>
├── processed/
│   ├── process.py
│   ├── process.log
│   └── <processed output files>
└── data_analysis/
    └── <user-chosen-analysis-name>/
        ├── analysis.py
        └── results.txt
```

Use this layout when the data root contains multiple distinct datasets:

```text
data/
├── <dataset-name>/
│   ├── raw/
│   │   ├── download.py
│   │   ├── download.log
│   │   └── <downloaded source files>
│   ├── processed/
│   │   ├── process.py
│   │   ├── process.log
│   │   └── <processed output files>
│   └── data_analysis/
│       └── <user-chosen-analysis-name>/
│           ├── analysis.py
│           └── results.txt
└── <another-dataset-name>/
    ├── raw/
    ├── processed/
    └── data_analysis/
```

Use short, stable, filesystem-safe dataset directory names. Do not add a dataset-name level for a single dataset unless the user requests it or the existing project already follows that convention.

Do not mix the single-dataset and multi-dataset layouts in one data root. When a second dataset is added to an existing single-dataset tree, reorganize the existing dataset under `data/<existing-dataset-name>/` before adding the new dataset. Preserve all existing files and ask for the existing dataset name only when it cannot be determined safely.

## Download raw data

Place each dataset's download implementation in its `raw/download.py`.

- Make the download reproducible and safe to rerun.
- Save original source files only inside the corresponding `raw/` directory.
- Preserve downloaded source files unchanged; do not overwrite or transform them during post-processing.
- Do not hard-code passwords, API keys, access tokens, or other credentials. Use the environment or the user's existing authenticated tooling.
- Write download progress and outcomes to `raw/download.log`, including timestamps, sources, destinations, completion status, and actionable error details. Record file sizes or checksums when they materially help verify integrity.
- Make `download.py` configure and write its sibling `download.log`; do not rely on an unrelated top-level log.
- If downloading requires credentials, acceptance of terms, substantial cost, or another user-authorized external action, stop at the relevant boundary and obtain the required authorization.

## Process data

Place each dataset's post-processing implementation in its `processed/process.py`.

- Implement the transformations required by the user's model-development task rather than imposing a generic pipeline.
- Read source data from the matching `raw/` directory and write generated data only to the matching `processed/` directory.
- Keep processing reproducible and safe to rerun. Use explicit parameters or a deterministic seed when randomness is required.
- Write a detailed execution record to `processed/process.log`, including timestamps, input paths, configured transformations, relevant counts or shapes, output paths, completion status, and actionable error details.
- Make `process.py` configure and write its sibling `process.log`; do not rely on an unrelated top-level log.
- Keep intermediate and final processed artifacts in `processed/`; do not modify the original raw files.

## Analyze data

Create `data_analysis/` only when the user requests an analysis. Place it beside the matching `raw/` and `processed/` directories:

- For a single dataset, use `data/data_analysis/`.
- For multiple datasets, use `data/<dataset-name>/data_analysis/` for the dataset being analyzed.

Inside `data_analysis/`, create one subdirectory for each analysis. The user chooses its name, for example `data_analysis/solvent_kinds/`. If no analysis name is provided, ask for one instead of inventing it.

Each analysis subdirectory must contain only:

```text
<analysis-name>/
├── analysis.py
└── results.txt
```

- Implement in `analysis.py` only the analysis requested by the user. Use another script extension only when the user explicitly chooses another language.
- Read the appropriate raw or processed inputs without modifying them. Prefer processed inputs when the requested analysis depends on cleaned or transformed data.
- Write the method, input paths, parameters, key findings, completion status, and actionable errors to `results.txt`.
- If the user requests only an analysis scaffold, initialize `results.txt` with a clear `NOT_RUN` status.
- Do not create notebooks, extra logs, temporary exports, plots, or additional result files unless the user explicitly requests them.
- For multiple analysis questions, create separate user-named subdirectories rather than combining unrelated analyses.
- If one analysis spans multiple datasets and the owning dataset directory is unclear, ask the user where the analysis directory should live.

## Work with existing projects

Inspect the current tree before creating files. Preserve compatible existing data, scripts, logs, and project conventions. Add missing pieces or make the smallest necessary update instead of deleting or silently replacing user files.

When the user asks to execute the workflow, run the relevant scripts and verify their outputs and logs. When the request only calls for scaffolding or planning, create the required structure and scripts without initiating downloads or expensive processing; initialize each log with a clear `NOT_RUN` status instead of inventing progress or success.

In the final response, summarize the chosen single- or multi-dataset layout, the created or updated scripts, the generated data artifacts, the locations of both logs, and any analysis directory with its `results.txt`. Clearly report any download, processing, or analysis step that was not run.
