# CNV Pre-merge Preparation (Nextflow)

A Nextflow DSL2 module designed to preprocess and standardize results from various CNV calling packages. It orchestrates a four-step workflow—utilizing Python-based normalization, filtering, and correction scripts—to harmonize data formats and quality before merging multi-caller outputs.

### ⚠️ Caution
> **Important:**  
> This workflow is designed to work only with output files from the following tools:  
> jabCoNtool, cnMOPS, cnvKit, control_freec, ExomeDepth, GATK  
>
> Only `.tsv` and `.cns` file formats are supported.

---

## Pipeline Overview

| Step | Script | Description |
|------|--------|------------|
| 1 | `1_trimming_and_normalization.py` | Trims unwanted contigs and normalizes headers of input files |
| 2 | `2_column_filtering.py` | Keeps only relevant columns (`CHROM`, `START`, `END`, `CN_ESTIMATE`, `TYPE`) |
| 3 | `3_cn_rounding_and_corrections.py` | Rounds copy-number estimates and applies corrections |
| 4 | `TYPE_NORMALIZATION` | Performs final type normalization |
| 5 | `TRACE_HANDLER` | Moves final outputs to top-level results folder; cleans intermediates if `trace=false` |

---

## Requirements

- **Nextflow** >= 25.10  
- **Python 3.10+**  

---

## Project Structure
```text
project/
├── main.nf
├── nextflow.config
├── bin/
│   ├── 1_trimming_and_normalization.py
│   ├── 2_column_filtering.py
│   ├── 3_cn_rounding_and_corrections.py
│   └── 4_type_normalization.py
├── input_files/
│   ├── sample1.tsv
│   └── sample2.cns
└── results/
```

- All Python scripts are in `bin/`  
- Input files go in `input_files/`  
- Outputs are published in `results/`  

---

## Configuration

**`nextflow.config`** defines default parameters:

```groovy
params {
    input_dir = "input_files"   // Directory with input files
    trace     = false            // true = keep all intermediate results, false = keep only final results
}
```

## Before running the pipeline
You should ensure all scripts in bin/ are executable:
```
chmod +x bin/*.py
```

## Running the Pipeline

Basic run using defaults:
```
nextflow run main.nf
```

Override input directory and trace:
```
nextflow run main.nf --input_dir input_files --trace true
```

Resume previous run:
```
nextflow run main.nf -resume
```

## Result
Final results are stored in:
```
results/
```

If `trace=true`, intermediate directories are preserved:
```text
results/
├── 1_trimming_and_normalization/
├── 2_column_filtering/
├── 3_cn_rounding_and_corrections/
└── 4_type_normalization/
```

If `trace=false`, only the final results are kept and intermediate directories are removed.
```text
results/
├── sample_1.tsv
├── sample_2.cns
└── sample_3.tsv
```

The naming of the result files are reflected their directory structure. 
For example:
```
folder1/sub_folder2/file.tsv → folder1_sub_folder2_file.tsv
file.tsv → file.tsv
```

---

# 🐍 Using the Pipeline as a Module
You can integrate the 4-step structural variant workflow into a bigger Nextflow pipeline as a **reusable module**.
Organize your module like this:

## Put the workflow in a module folder
```
modules/
└── cnv-premerge-prep/
    ├── main.nf           # workflow definition
    ├── nextflow.config
    ├── input_files/
    └── bin/              # Python scripts (1_trimming_and_normalization.py, etc.)
```

## Import the module in your main pipeline
In your main `main.nf` file, include the workflow from the module:
```
include { cnv_premerge_prep } from './modules/cnv-premerge-prep/main.nf'
```

## Set parameters
Define the parameters that will be passed to the module workflow:
```
params.input_dir = "input_files"   // folder containing your input files
params.trace     = false           // true = keep intermediate results, false = keep only final outputs
```

## Call the module workflow in your main workflow
```
workflow {

    results = cnv_premerge_prep(
        input_dir: params.input_dir,
        trace_flag: params.trace
    )

    // You can continue using 'results' as input for other steps in your main workflow
}
```

