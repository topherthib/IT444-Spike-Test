# Raspberry Pi 5 retraining spike

## Question and targets

Can a Raspberry Pi 5 retrain the classifier of a MobileNetV3 Small deepfake
detector locally within the time and memory budgets while retaining video-level
detection performance?

The round had to train and save within **300 seconds**, use at most **2,048 MiB**
of peak process memory, and produce a checkpoint that could be reloaded. At a
fixed decision threshold of **0.55**, the false-positive and false-negative rates
could each increase by at most **5 percentage points**.

## How the test was run

Run `run-ZPtKAM` started from the refined MobileNetV3 Small checkpoint
`mobilenet_v3_small_refinement_20261029_151726`. The Pi froze the feature
extractor and trained the classifier on the CPU for one epoch. It used 256
pre-cropped FaceForensics++ images (128 real and 128 fake), a batch size of 8,
four CPU threads, and seed 42. The
[training subset](results/run-ZPtKAM/training_subset.csv) lists the exact crops.

The timed interval included crop loading and preprocessing, training, and
checkpoint saving. The script then tried to reload the checkpoint. The
[training results](results/run-ZPtKAM/training_results.json) record elapsed time,
average process CPU use, peak process memory, and whether the checkpoint reloaded.
A separate [hardware log](results/run-ZPtKAM/hardware-20261112-114954.log)
sampled system-wide activity across the training and evaluation run.

Both checkpoints were evaluated on the same 40 held-out videos (20 real and
20 fake, with 635 crops total). The evaluator averaged each video's crop-level
fake probabilities and classified the video as fake if the average reached
0.55. It saved an [evaluation summary](results/run-ZPtKAM/evaluation_results.json)
and [per-video predictions](results/run-ZPtKAM/video_predictions.csv).

## Results

Run `run-ZPtKAM` met all five targets.

| Measure | Pass target | Run result | Outcome |
|---|---|---|---|
| Train and save time | ≤ 300 s | 5.01 s | Pass |
| Peak process memory | ≤ 2,048 MiB | 781.0 MiB | Pass |
| Checkpoint reload | Successful | Reloaded successfully | Pass |
| False-positive rate | Increase of at most 5 percentage points | 0% → 0% (0 → 0 of 20 real videos) | Pass |
| False-negative rate | Increase of at most 5 percentage points | 45% → 25% (9 → 5 of 20 fake videos) | Pass |

Video accuracy rose from **77.5% to 87.5%** (31 to 35 correct out of 40). The
training process used an average of **3.63 of four CPU cores** during the timed
interval. Across the combined hardware trace, the peak sampled temperature was
**64.2 °C**, and every throttling reading was `0x0`. The log recorded zero swap
activity.

This result supports keeping the Pi 5 as a candidate for local classifier
updates. The next IT 445 test should measure inference latency while a Flower
client performs a local update on the same Pi.
