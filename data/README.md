
## Input Data Format

### `signal_sorting.csv`

Place the CSV file under `data/signal_sorting.csv`. The file must contain the following **10 columns**:

```csv
Sequence,CH,GPI,MT,NES,NLS,PTS,SP,TH,TM
MYWSNQITRRLGERV...,0,1,0,0,0,0,1,0,0
MAAKLVFASSGQKFQ...,0,0,1,0,1,0,0,0,0
```

| Column | Description |
|--------|-------------|
| `Sequence` | Amino acid sequence (string) |
| `CH` | Chloroplast transit peptide |
| `GPI` | GPI-anchor signal |
| `MT` | Mitochondrial targeting signal |
| `NES` | Nuclear Export Signal |
| `NLS` | Nuclear Localization Signal |
| `PTS` | Peroxisomal Targeting Signal |
| `SP` | Signal Peptide |
| `TH` | Thylakoidal transfer peptide |
| `TM` | Transmembrane segment |

- Label values: `0` or `1` (multi-label, multiple columns can be `1` simultaneously)
- Maximum sequence length: 512 tokens
- Default sample size: 1,868 sequences

---
