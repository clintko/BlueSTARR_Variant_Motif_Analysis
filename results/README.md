# Results

This folder contains finalized result tables and figures for the BlueSTARR variant motif analysis.

```text
results/
├── figures/
└── tables/
```

## Result tables

`tables/variant_closed_gof_bluestarr.flankL35R70.unobs_vs_obs.pilot.motif.enrich.tsv`

This table contains motif enrichment statistics for closed-region variants with predicted gain of regulatory activity by BlueSTARR.

The analysis compares:

- `Top` variants: top 100,000 variants ranked by descending BlueSTARR delta score.
- `Low` variants: lowest 100,000 variants ranked by BlueSTARR delta score.
- `Ori`: original variant-centered sequences.
- `Nuc`: dinucleotide-shuffled control sequences generated from the original sequences while preserving the variant.

The motif count table therefore contained motif gain and loss counts for each combination of variant group and sequence type:
```
Top_Ori
Top_Nuc
Low_Ori
Low_Nuc
```

For each motif, motif gain and motif loss events were analyzed separately.

## Statistical model

For each motif and each direction (`Count_Gain` or `Count_Loss`), a binomial logistic regression model was fit using motif event counts:

```
glm(
    cbind(Count, Count_Total - Count) ~ Group * Type,
    family = binomial
)
```

where:

- `Group` compares `Top` versus `Low` variants.
- `Type` compares original sequences (`Ori`) versus dinucleotide-shuffled controls (`Nuc`).
- `Group * Type` tests whether the enrichment in top variants differs between original and shuffled sequences.

The reported coefficient is the interaction term `GroupTop:TypeOri`. This interaction term estimates motif enrichment in top variants after accounting for the dinucleotide-shuffled sequence background.

## Columns:

- `Direction`: motif event type, either `Count_Gain` or `Count_Loss`.
- `Motif_Name`: motif identifier and motif family/name.
- `Beta_Est`: estimated coefficient for the `GroupTop:TypeOri` interaction term.
- `OddsRatio`: odds ratio, calculated as `exp(Beta_Est)`.
- `Beta_SE`: standard error of the coefficient.
- `Beta_Z`: z statistic, calculated as `Beta_Est / Beta_SE`.
- `Beta_Pval`: nominal p-value for the interaction coefficient.
- `Beta_Padj`: Benjamini-Hochberg adjusted p-value, adjusted within each motif direction.
- `Log10Beta`: coefficient converted to log10 scale, calculated as `Beta_Est / log(10)`.
- `NLog10Padj`: negative log10 adjusted p-value, calculated as `-log10(Beta_Padj)`.


## Interpretation

A positive `Beta_Est` or `OddsRatio > 1` indicates that the motif event is enriched in top BlueSTARR variants relative to low variants, after accounting for dinucleotide-shuffled sequence controls.

Likewise, a negative `Beta_Est` or `OddsRatio < 1` indicates depletion in top variants relative to low variants after sequence-background correction.

