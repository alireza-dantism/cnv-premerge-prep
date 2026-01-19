# CNV Pre-merge Preparation (Nextflow)

This pipeline performs **structural variant calling** and copy-number analysis in **four steps**, using Python scripts and Nextflow DSL2.

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
- Optional: **Conda** or Docker (for reproducible environments)  

---

## Project Structure
```text
project/
├── main.nf
├── nextflow.config
├── bin/
│   ├── 1_trimming_and_normalization.py
│   ├── 2_column_filtering.py
│   └── 3_cn_rounding_and_corrections.py
├── input_files/
│   ├── sample1.tsv
│   └── sample2.cns
├── results/
└── README.md
```

