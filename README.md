# Raspberry Pi 5 retraining spike

Run: run-ZPtKAM

The Raspberry Pi 5 ran one classifier-only epoch starting from the October 29
refined MobileNetV3 Small checkpoint (mobilenet_v3_small_refinement_20261029_151726).
The round used 256 pre-cropped face images, a batch size of
8, and 4 CPU threads.

## Training results

| Measure | Result |
|---|---:|
| Train and save time | 5.01 s |
| Average CPU cores used | 3.63 |
| Peak training process memory | 781.0 MiB |
| Saved checkpoint reloaded | True |

## Video evaluation

Both checkpoints were scored on the same 40 held-out videos
(635 crops) at a fixed threshold of 0.55.

| Measure | Before retraining | After retraining |
|---|---:|---:|
| Accuracy | 77.5% | 87.5% |
| False alarms | 0 | 0 |
| Missed deepfakes | 9 | 5 |

Time target passed: True.
Memory target passed: True.
Retention criterion passed: True.

## Evidence

- [Training metrics](results/run-ZPtKAM/training_results.json)
- [Evaluation summary](results/run-ZPtKAM/evaluation_results.json)
- [Per-video scores and predictions](results/run-ZPtKAM/video_predictions.csv)
- [Exact training crop list](results/run-ZPtKAM/training_subset.csv)
