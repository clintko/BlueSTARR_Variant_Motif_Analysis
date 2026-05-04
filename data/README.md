# Data

This folder contains input variant sets used for the BlueSTARR variant motif analysis.

## Input variants

The main analysis compares two variant sets:

- `input_variants/variant_closed_gof_bluestarr_top100k.tsv`
  - Top 100,000 closed-region gain-of-function variants ranked by BlueSTARR delta score.

- `input_variants/variant_closed_gof_bluestarr_low100k.tsv`
  - Lowest 100,000 closed-region variants ranked by BlueSTARR delta score, used as the comparison/background set.

## Columns

- `Chrom`: chromosome
- `ChromStart`: start coordinate of the sequence region
- `ChromEnd`: end coordinate of the sequence region
- `Pos0`: 0-based variant position
- `Ref`: reference allele
- `Obs`: observed allele
- `Unobs`: unobserved allele
- `Delta`: predicted change in regulatory activity from BlueSTARR