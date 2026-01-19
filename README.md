# CNV Pre-merge Preparation (Nextflow)

A Nextflow DSL2 module designed to preprocess and standardize results from various CNV calling packages. It orchestrates a four-step workflow—utilizing Python-based normalization, filtering, and correction scripts—to harmonize data formats and quality before merging multi-caller outputs.

---

## Pipeline Overview

| Step | Script | Description |
|------|--------|------------|
| 1 | `1_trimming_and_normalization.py` | Trims unwanted contigs and normalizes headers of input files |
| 2 | `2_column_filtering.py` | Keeps only relevant columns (`CHROM`, `START`, `END`, `CN_ESTIMATE`, `TYPE`) |
| 3 | `3_cn_rounding_and_corrections.py` | Rounds copy-number estimates and applies corrections |
| 4 | `TYPE_NORMALIZATION` | Performs final type normalization (optional) |
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

## Running the Pipeline

### Basic run using defaults
```
nextflow run main.nf
```

### Override input directory and trace
```
nextflow run main.nf --input_dir input_files --trace true
```

### Resume previous run
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
└── 4_final_results/
```

If `trace=false`, only the final results are kept and intermediate directories are removed.






