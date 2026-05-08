# Results

This folder contains finalized result tables and figures for the BlueSTARR variant motif analysis.

```text
results/
├── README.md
├── figures/
└── tables/
```

- `figures/`: summary figures generated from the motif perturbation and enrichment analysis.
- `tables/`: finalized statistical result tables.

## Result tables

```
tables/variant_closed_gof_bluestarr.flankL35R70.unobs_vs_obs.pilot.motif.enrich.tsv
```

This table contains motif enrichment statistics for closed-region variants with predicted gain of regulatory activity by BlueSTARR. The analysis compares motif gain and loss events across two variant groups and two sequence types:

- `Top`: top 100,000 variants ranked by descending BlueSTARR delta score.
- `Low`: lowest 100,000 variants ranked by BlueSTARR delta score.
- `Ori`: original variant-centered sequences.
- `Nuc`: dinucleotide-shuffled control sequences generated from the original sequences while preserving the variant.

The motif count table therefore contained motif gain and loss counts for four conditions:

```text
Top_Ori
Top_Nuc
Low_Ori
Low_Nuc
```

For each motif, motif gain and motif loss events were analyzed separately.

## Motif count table

The motif enrichment table was generated from a motif count table with the following columns:

- `Group_Type`: combined label for variant group and sequence type, such as `Top_Ori` or `Low_Nuc`.
- `Group`: variant group, either `Top` or `Low`.
- `Type`: sequence type, either `Ori` or `Nuc`.
- `Motif_Name`: motif identifier/name.
- `Count_Total`: total number of variants evaluated for the motif.
- `Count_Gain`: number of variants with at least one motif gain event.
- `Count_Loss`: number of variants with at least one motif loss event.

The motif count table was converted into long format so that `Count_Gain` and `Count_Loss` could be modeled separately.

For more information of the motif perturbation analysis and motif gain/loss event definition, see `notebooks/README.md`

## Statistical model

For each motif and each motif event direction (`Count_Gain` or `Count_Loss`), a logistic regression model was fit using motif event counts:

```r
glm(
    cbind(Count, Count_Total - Count) ~ Group * Type,
    family = binomial
)
```

Before fitting the model, a small pseudo-count was added:

```r
Count = Count + 0.5
Count_Total = Count_Total + 1
```

This reduces instability caused by zero counts in the binomial model.

In the model:

- `Group` compares `Top` versus `Low` variants.
- `Type` compares original sequences (`Ori`) versus dinucleotide-shuffled controls (`Nuc`).
- `Group * Type` tests whether the difference between top and low variants depends on sequence type, comparing original sequences against dinucleotide-shuffled controls.

The reported coefficient is the interaction term:

```text
GroupTop:TypeOri
```

This interaction term estimates motif enrichment in top variants after accounting for dinucleotide-shuffled sequence background.

## Columns in the enrichment result table

- `Direction`: motif event type, either `Count_Gain` or `Count_Loss`.
- `Motif_Name`: motif identifier and motif family/name.
- `Beta_Est`: estimated coefficient for the `GroupTop:TypeOri` interaction term.
- `OddsRatio`: odds ratio, calculated as `exp(Beta_Est)`.
- `Beta_SE`: standard error of the coefficient.
- `Beta_Z`: z statistic, calculated as `Beta_Est / Beta_SE`.
- `Beta_Pval`: nominal p-value for the interaction coefficient.
- `Beta_Padj`: Benjamini-Hochberg adjusted p-value. P-values were adjusted separately within each motif event direction.
- `Log10Beta`: coefficient converted to log10 scale, calculated as `Beta_Est / log(10)`.
- `NLog10Padj`: negative log10 adjusted p-value, calculated as `-log10(Beta_Padj)`.

## Interpretation

A positive `Beta_Est` or `OddsRatio > 1` indicates that the motif gain or loss event is more enriched in top variants than in low-score variants in original sequences relative to the same comparison in dinucleotide-shuffled controls.

A negative `Beta_Est` or `OddsRatio < 1` indicates depletion in top variants relative to low-score variants after sequence-background correction.

In this analysis, enriched motif gain events can suggest creation or strengthening of transcription factor binding sites among variants with predicted gain of regulatory activity. Enriched motif loss events may imply disruption or weakening of transcription factor binding sites among variants with predicted gain of regulatory activity.
