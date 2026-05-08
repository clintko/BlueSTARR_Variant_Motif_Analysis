# Data

This folder contains input variant subsets used for the BlueSTARR variant motif analysis.

```text
data/
├── README.md
└── input_variants/
    ├── variant_closed_gof_bluestarr_top100k.tsv
    └── variant_closed_gof_bluestarr_low100k.tsv
```

## Input variants

The main analysis compares two variant sets selected from 1,885,038 closed-region variants after sorting by descending BlueSTARR delta score.

- `input_variants/variant_closed_gof_bluestarr_top100k.tsv`
  - Top 100,000 closed-region variants ranked by descending BlueSTARR delta score.
  - These variants represent the strongest predicted increases in regulatory activity in the closed-region variant set.

- `input_variants/variant_closed_gof_bluestarr_low100k.tsv`
  - Lowest 100,000 closed-region variants ranked by BlueSTARR delta score.
  - These variants are used as the low-`Delta`/background comparison set.

## Observed and unobserved alleles

BlueSTARR was used to compare predicted regulatory activity between observed and unobserved alleles at genomic sites in gnomAD.

For each genomic site, the observed allele (`Obs`) was defined as the allele with the highest allele frequency among the reference allele and single-nucleotide variants reported in gnomAD. In many cases, the observed allele is the reference allele. However, when a variant allele is more frequent than the reference allele in the population, that variant allele was treated as the observed allele.

The unobserved allele (`Unobs`) was defined as an alternative nucleotide at the same genomic position that was not observed among the known alleles considered at that site. For each possible unobserved allele, predicted regulatory activity was computed using BlueSTARR. The unobserved allele used in the downstream analysis was the allele that produced the largest predicted increase in regulatory activity relative to the observed allele.

The BlueSTARR delta score (`Delta`) represents the predicted change in regulatory activity between the unobserved and observed alleles:

```text
Delta = predicted activity(Unobs) - predicted activity(Obs)
```

Variants with large positive `Delta` values are interpreted as variants with predicted gain of regulatory activity.

## Columns

The input variant tables contain the following columns:

- `Chrom`: chromosome.
- `ChromStart`: start coordinate of the sequence region.
- `ChromEnd`: end coordinate of the sequence region.
- `Pos0`: 0-based variant position.
- `Ref`: reference allele.
- `Obs`: observed allele.
- `Unobs`: unobserved allele.
- `Delta`: predicted change in regulatory activity from BlueSTARR.

## Notes

The full input variant table contains 1,885,038 variants. This repository includes only the top 100,000 and lowest 100,000 closed-region variant sets used for the motif enrichment analysis.
