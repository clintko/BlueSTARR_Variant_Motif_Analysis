# BlueSTARR Variant Motif Analysis

## Introduction

Motif perturbation and enrichment analysis for BlueSTARR-identified regulatory variants.

This repository contains input variant sets, scripts, and documentation for motif gain/loss and enrichment analysis of BlueSTARR-predicted regulatory variants.

- `data/`: input variants used for the analysis
- `scripts/`: analysis scripts for motif scanning, motif delta calculation, enrichment analysis
- `notebooks/`: exploratory or analysis notebooks

## Main input data

## Analysis workflow

1. Prepare BlueSTARR variant input sets
2. Prepare motif files
3. Scan motifs in variant sequences 
4. Calculate motif gain/loss events
5. Merge motif-level results
6. Run enrichment analysis
7. Generate summary tables and figures


### Prepare BlueSTARR variant input sets

Set sequence windows for each variants, controling nucleotide composition (e.g. GC content using dinuc shuffling

