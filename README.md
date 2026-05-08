# BlueSTARR Variant Motif Analysis

## Introduction

This repository contains input variant sets, scripts, notebooks, result tables, and figures for motif perturbation and enrichment analysis of BlueSTARR-predicted regulatory variants.

This project builds on a collaboration among the Majoros, Reddy, and Allen labs to model the regulatory effects of genetic variants using BlueSTARR.

The related BlueSTARR work is available on bioRxiv:

[Modeling gene regulatory perturbations via deep learning from high-throughput reporter assays](https://www.biorxiv.org/content/10.64898/2026.03.27.714770v1)

BlueSTARR is a deep learning model trained on whole-genome STARR-seq data from K562 and A549 cells to predict regulatory activity from DNA sequence. Using this model, variant sites from gnomAD were evaluated to identify allelic changes predicted to shift regulatory activity.

In this analysis, I focus on variants where the unobserved allele is predicted to increase regulatory activity relative to the observed allele. That is, those analyzed variants have predicted gain of regulatory activity. 

To provide a mechanistic explanation behind such shift of regulatory activity, I performed motif perturbation and enrichment analysis to examine whether the gain or loss of specific transcription factor motif bindings are enriched among these variants.

## Repository structure

```text
BlueSTARR_Variant_Motif_Analysis/
├── container/
├── data/
├── notebooks/
├── results/
│   ├── figures/
│   └── tables/
├── scripts/
└── README.md
```

- `container/`: Apptainer definition file for the analysis environment.
- `data/`: input variant sets used for the analysis.
- `scripts/`: scripts for motif preparation, motif scanning, motif delta calculation, and enrichment analysis.
- `notebooks/`: exploratory and workflow notebooks used during the motif analysis.
- `results/`: result tables and figures.

## Analysis overview

The analysis starts from BlueSTARR-predicted variant effects. Variants are ranked by the predicted change in regulatory activity between the unobserved and observed alleles:

```text
Delta = predicted activity(Unobs) - predicted activity(Obs)
```

The main analysis compares:

- Top 100,000 closed-region variants ranked by descending BlueSTARR `Delta`.
- Lowest 100,000 closed-region variants ranked by BlueSTARR `Delta`.

For each variant, allele-specific sequence windows are generated for the observed and unobserved alleles. Transcription factor motifs are scanned in these sequences, and motif gain/loss events are defined by comparing motif binding status between alleles.

To control for local nucleotide composition, dinucleotide-shuffled control sequences are generated and analyzed in parallel. Motif gain/loss events are then tested for enrichment among top-ranked variants relative to low-`Delta` variants.

## Analysis workflow

The main workflow is:

1. Prepare BlueSTARR variant input sets.
2. Define variant-centered sequence windows.
3. Generate dinucleotide-shuffled control sequences.
4. Prepare motif files.
5. Scan motifs in original and shuffled variant sequences.
6. Calculate motif gain and loss events.
7. Merge motif-level results.
8. Run motif enrichment analysis.
9. Generate summary tables and figures.

## Input data

The main input variant sets are stored in:

```text
data/input_variants/
```

This repository includes the top 100,000 and lowest 100,000 closed-region variant sets used for the motif enrichment analysis.

See `data/README.md` for details about the input variant files, variant selection, observed/unobserved allele definitions, and table columns.

## Notebooks and methods

The analysis notebooks are stored in:

```text
notebooks/analysis_variant_motif_richard/
```

The notebooks document the main analysis steps, including variant preparation, sequence export, motif file preparation, motif scanning, motif delta calculation, motif count summarization, and motif enrichment analysis.

See `notebooks/README.md` for the notebook organization and methods summary.

## Results

Finalized result tables and figures are stored in:

```text
results/
├── figures/
└── tables/
```

The main motif enrichment result table is:

```text
results/tables/variant_closed_gof_bluestarr.flankL35R70.unobs_vs_obs.pilot.motif.enrich.tsv
```

See `results/README.md` for details about the statistical model, result table columns, and interpretation.

## Environment

The analysis environment is documented in:

```text
container/Apptainer.def
```

This file defines the Apptainer/Singularity environment used for Python, R, Bioconductor, Jupyter, and motif-analysis dependencies.
