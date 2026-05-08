# Notebooks

This folder contains exploratory and workflow notebooks for the BlueSTARR variant motif analysis.

The notebooks document the steps used to prepare variant-centered sequences, perform motif scanning, calculate motif gain/loss events, and run motif enrichment analysis.

```text
notebooks/
├── README.md
├── analysis_variant_motif_richard/
├── run_config_project.R
├── run_config_project.py
└── run_config_project.sh
```

- `analysis_variant_motif_richard/`: main motif analysis notebooks.
- `run_config_project.R`: R code configuration used by the notebooks.
- `run_config_project.py`: Python code configuration used by the notebooks.
- `run_config_project.sh`: shell code configuration used by the notebooks.

## Notebooks summary

The notebooks are numbered by analysis stage:

- `01–02`: import and explore BlueSTARR variant predictions.
- `11–19`: prepare variant tables, export allele-specific sequences, and prepare sequence batches.
- `21–24`: prepare motif background models and convert motif files.
- `31`: run motifdelta package tests.
- `41–42`: run motif scanning and motif delta calculation for top variants.
- `51–54`: run pilot motif scanning, motif delta calculation, merging, and exploration.
- `61–63`: prepare motif count tables, run motif enrichment analysis, and explore enrichment results.

## Methods summary

### 1. Variant selection

BlueSTARR was used to predict regulatory activity for observed (`Obs`) and unobserved (`Unobs`) alleles at gnomAD variant sites. The predicted regulatory effect was summarized as:

```text
Delta = predicted activity(Unobs) - predicted activity(Obs)
```

Variants were ranked by descending `Delta`. The analysis used the top 100,000 variants as the high-`Delta` set and the lowest 100,000 variants as the low-`Delta`/background comparison set.

For more information, see `data/README.md` for details about the input regulatory variants.

### 2. Sequence preparation

For each variant, a variant-centered sequence window was generated using:

```text
35 bp left flank + variant + 70 bp right flank
```

This allows me to get the motif scanning score by truncating the final vector as variant with 35 scores at both sides.

Allele-specific sequences were generated for the observed allele (`Obs`) and the unobserved allele (`Unobs`). Dinucleotide-shuffled control sequences were also generated from the original sequences to control for local nucleotide composition while preserving the variant.

### 3. Motif scanning and motif delta calculation

For each variant, transcription factor motifs were scanned across both allele-specific sequences using position weight matrices. Motif scanning was performed on both the forward and reverse strands.

For each motif and sequence position, a motif score was calculated to quantify how well the local sequence matched the motif model. Motif-specific binding thresholds were then used to determine whether a motif was significantly bound at a given position.

The motif score change between alleles was defined as:

```text
Delta_Motif = motif score(Unobs) - motif score(Obs)
```

A positive `Delta_Motif` indicates that the unobserved allele increases the motif score relative to the observed allele. A negative `Delta_Motif` indicates that the unobserved allele decreases the motif score.

Motif gain and loss events were defined by comparing binding status between the observed and unobserved allele sequences:

```text
Motif gain = Score(Unobs) >= threshold AND Score(Obs) < threshold
Motif loss = Score(Obs) >= threshold AND Score(Unobs) < threshold
```

Thus, a motif gain event represents the creation of a motif binding site in the unobserved allele under the motif-specific threshold definition, while a motif loss event represents the disruption of a motif binding site. 

Because multiple motif hits can occur within the same sequence window, motif perturbations were summarized at the variant-motif level. For each variant and motif, at most one gain event and one loss event were retained. The strongest gain event was represented by the largest positive `Delta_Motif`, and the strongest loss event was represented by the most negative `Delta_Motif`.

### 4. Motif enrichment analysis

Motif gain and loss events were summarized across four groups:

```text
Top_Ori
Top_Nuc
Low_Ori
Low_Nuc
```

where:

- `Top`: top 100,000 variants ranked by descending BlueSTARR `Delta`.
- `Low`: lowest 100,000 variants ranked by BlueSTARR `Delta`.
- `Ori`: original variant-centered sequences.
- `Nuc`: dinucleotide-shuffled control sequences.

For each motif, motif gain and motif loss events were analyzed separately. A binomial logistic regression model was used to test whether motif perturbation events were enriched in top variants after accounting for dinucleotide-shuffled sequence controls.

See `results/README.md` for details about the statistical model, result table columns, and interpretation.
