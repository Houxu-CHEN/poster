# Original EEGNet Results Summary

## Setup

- Model: original revised EEGNet, without local attention, temporal-block, multi-kernel, spectral, or asymmetry extensions.
- Main hyperparameters: `F1=8`, `D=2`, `F2=16`, temporal kernel `64`, separable kernel `16`, dropout `0.5`, Adam `lr=1e-3`, no weight decay.
- Metric for checkpoint selection: validation accuracy.
- Seed: `42`.

## Results

| Dataset | Task | Data Root | Epochs | Best Epoch | Best Val Acc | Test Acc | Test Kappa | Run Directory |
| --- | --- | --- | ---: | ---: | ---: | ---: | ---: | --- |
| BCICIV2A | 4-class motor imagery | `/vePFS-0x0d/home/yzt/full_bcic2a_courseval_data/BCIC2A` | 300 | 124 | 0.7917 | - | - | `runs/BCIC2A/eegnet_original/orig_eegnet_full_bcic2a_seed42_e300` |
| BCICIV2B | 2-class motor imagery | `/vePFS-0x0d/home/yzt/bcic2b_h5_data/BCIC2B` | 300 | 113 | 0.7772 | 0.7835 | 0.5669 | `runs/BCIC2B/eegnet_original/orig_eegnet_full_bcic2b_seed42_e300` |
| IIIa | 4-class motor imagery | `/vePFS-0x0d/home/yzt/mi4_datasets/processed/IIIa` | 100 | 50 | 0.5595 | - | - | `runs/IIIa/eegnet_original/orig_eegnet_iiia_fast_e100_seed42` |
| Physionet_MI-22ch | 4-class motor imagery | `/vePFS-0x0d/home/yzt/mi4_datasets/processed_bcic22/Physionet_MI` | 100 | 76 | 0.4930 | - | - | `runs/Physionet_MI/eegnet_original/orig_eegnet_physionet22_fast_e100_seed42` |

## BCICIV2B Test Details

Best checkpoint:

- `/vePFS-0x0d/home/yzt/-2026-/runs/BCIC2B/eegnet_original/orig_eegnet_full_bcic2b_seed42_e300/checkpoints/best.pt`

Test file:

- `/vePFS-0x0d/home/yzt/bcic2b_h5_data/BCIC2B/test_labeled.h5`

Test split:

- Shape: `(2840, 3, 1000)`
- Label counts: `1420/1420`
- Accuracy: `0.7834507042`
- Balanced accuracy: `0.7834507042`
- Macro F1: `0.7833634383`
- Kappa: `0.5669014085`
- Confusion matrix: `[[1084, 336], [279, 1141]]`

Evaluation artifact:

- `/vePFS-0x0d/home/yzt/-2026-/runs/BCIC2B/eegnet_original/orig_eegnet_full_bcic2b_seed42_e300/test_labeled_eval.json`

## Interpretation

BCICIV2A and BCICIV2B are the cleanest results to report because they were run for 300 epochs and match the BCI Competition motor-imagery setting most directly. IIIa and Physionet_MI-22ch are quick additional baselines; they are useful as supplementary evidence that the original EEGNet implementation runs across multiple MI datasets, but they should not be over-claimed because they used only a single seed and 100-epoch quick runs.

