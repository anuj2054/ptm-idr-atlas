# PTM-IDR Atlas v1.0.0

This directory contains the packaged PTM-IDR Atlas v1.0.0 artifacts.

## Contents

### Core Tables

| File | Format | Size | Description |
|------|--------|------|-------------|
| `hotspot_windows_annotated.{tsv,parquet}` | TSV/Parquet | 1.9 MB / 140 KB | Annotated hotspot windows with IDR localization and significance flags |
| `hotspot_protein_summary.{tsv,parquet}` | TSV/Parquet | 15 KB / 13 KB | Per-protein hotspot counts (total, IDR-localized, significant) |
| `hotspots.{tsv,parquet}` | TSV/Parquet | 400 KB / 143 KB | Per-protein hotspot statistics with FWER correction |
| `ptm_enrichment.{tsv,parquet}` | TSV/Parquet | 13 MB / 2.6 MB | PTM enrichment analysis (IDR vs ordered regions) |

### Validation Datasets

| File | Format | Size | Description |
|------|--------|------|-------------|
| `iptmnet_overlap.{tsv,parquet}` | TSV/Parquet | 2.2 KB / 9.6 KB | IPTMNet validation overlap statistics |
| `motif_overlap.{tsv,parquet}` | TSV/Parquet | 57 KB / 36 KB | ELM motif enrichment in hotspot regions |
| `variant_proximity.{tsv,parquet}` | TSV/Parquet | 1.5 MB / 304 KB | Clinical variant proximity to hotspot windows |

### Metadata

| File | Description |
|------|-------------|
| `manifest.json` | Full metadata including configuration, input snapshots, and file listing |
| `checksums.sha256` | SHA256 checksums for all artifacts |

## Quick Start

### Download and Verify

```bash
# Download artifacts
wget https://github.com/anuj2054/ptm_idr/raw/main/results/atlas/v1.0/hotspots.tsv
wget https://github.com/anuj2054/ptm_idr/raw/main/results/atlas/v1.0/checksums.sha256

# Verify integrity
sha256sum -c checksums.sha256
```

### Load in Python

```python
import pandas as pd

# Read TSV format
df = pd.read_csv('hotspots.tsv', sep='\t')

# Or read Parquet format (faster, smaller)
df = pd.read_parquet('hotspots.parquet')

print(f"Loaded {len(df)} proteins")
```

### Load in R

```r
# Read TSV format
df <- read.delim('hotspots.tsv')

# Or read Parquet format (requires arrow package)
library(arrow)
df <- read_parquet('hotspots.parquet')

cat("Loaded", nrow(df), "proteins\n")
```

## Configuration

This atlas was generated with the following parameters:

- **Window sizes**: 9, 15, 21 residues
- **Hotspot thresholds**: M = 2, 3 PTMs per window
- **Permutations**: 10,000 (for FWER control)
- **IDR threshold**: 50% disorder consensus
- **Significance threshold**: q ≤ 0.10 (FDR-corrected)
- **PTM sources**: UniProt only
- **Disorder predictor**: MetaPredict

See `manifest.json` for complete configuration details.

## Column Descriptions

### hotspot_windows_annotated

- `accession`: UniProt accession
- `W`: Window size (9, 15, or 21)
- `M`: Minimum PTMs required (2 or 3)
- `start`, `end`: Window coordinates (1-indexed)
- `count`: Number of PTMs in window
- `idr_frac`: Fraction of window residues in IDR
- `p_window_meet`: Per-window empirical p-value
- `q_window_meet_global`: Global FDR-corrected q-value
- `ptm_count`: Alias of `count`
- `is_candidate_window`: Always True for this table
- `is_idr_localized`: True if `idr_frac >= 0.5`
- `region_label`: "IDR" or "ORD"
- `is_significant_q10`: True if `q_window_meet_global <= 0.10`

### hotspot_protein_summary

- `accession`: UniProt accession
- `n_candidate_windows_total`: Total candidate windows for this protein
- `n_candidate_windows_idr`: Candidate windows with IDR localization
- `n_hotspots_q10_total`: Significant hotspots (q ≤ 0.10)
- `n_hotspots_q10_idr`: Significant hotspots in IDR regions
- `n_hotspots_q10_ord`: Significant hotspots in ordered regions

### hotspots

- `accession`: UniProt accession
- `W`: Window size
- `M`: Minimum PTMs threshold
- `obs_hotspots`: Observed number of hotspots
- `obs_idr_hotspots`: Observed IDR-localized hotspots
- `empirical_p_idr_enrichment`: Family-wise error rate corrected p-value

## Citation

If you use this atlas, please cite:

```
anuj2054/ptm_idr: PTM-IDR Atlas v1.0.0
DOI: 10.5281/zenodo.XXXXXXX (pending)
```

## Documentation

- [Atlas Site Guide](../../../docs/atlas_site.md) - GitHub Pages hosting instructions
- [Release Checklist](../../../docs/release_checklist.md) - Release process documentation
- [Repository README](../../../README.md) - Full pipeline documentation

## Version History

- **v1.0.0** (2025-11-05): Initial release
  - Full human proteome analysis
  - 10,000 permutation tests for FWER control
  - Clinical validation with ClinVar
  - Motif enrichment with ELM
  - IPTMNet validation
