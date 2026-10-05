# incremental-sfm-datasets

This repository contains image sequence datasets captured across the Nanyang Technological University (NTU) campus, specifically curated for evaluating and benchmarking Structure from Motion (SfM) pipelines.

## Companion Repositories

This data repository is part of a broader project suite for 3D computer vision evaluation:

* **[`incremental-sfm`](https://github.com/chuo-v/incremental-sfm):** The core incremental SfM engine and modular pipeline implementation.
* **[`incremental-sfm-experiments`](https://github.com/chuo-v/incremental-sfm-experiments):** The analytical workspace containing evaluation notebooks, metric logs, parameter tuning records, and comparative benchmarks.

## Datasets Overview

### 1. Nantah Memorial ([`nantah-memorial-P42`](./nantah-memorial-P42/))

* **The Subject:** The historic Nantah Memorial, a tall, narrow reinforced concrete monument designed in the Chinese National style, featuring an octagonal base and a traditional double-tiered roof with glazed tiles.
* **SfM Challenge:** Evaluates geometric tracking around a narrow central axis where dense orbital camera steps create shallow parallax angles. The dataset tests the pipeline's ability to filter out dynamic occlusion from foreground branches and swaying leaves while maintaining a rigid lock on the underlying concrete structure and roof motifs.

### 2. Yunnan Garden Pavilion ([`yunnan-garden-pavilion-P71`](./yunnan-garden-pavilion-P71/))

* **The Subject:** A traditional octagonal pavilion featuring highly symmetric architecture, identical red pillars, and repeating lattice patterns.
* **SfM Challenge:** Specifically designed to test a feature matcher's ability to resolve extreme architectural symmetry and severe depth parallax without starving the sequential tracker, while simultaneously filtering out heavy background foliage.
* **Known Baseline Behaviors:** This is an aggressively difficult sequence designed to stress-test feature extraction. Due to the combination of severe symmetry, repetitive textures, and privacy-compliant blurring that acts as a low-pass filter, the dataset exposes the limitations of classical, gradient-based extractors (e.g., SIFT), which are highly prone to feature starvation and subsequent minor relative scale drift in these localized segments. Conversely, learned descriptors (e.g., SuperPoint) with broad contextual receptive fields demonstrate resilience to these conditions, successfully tracking through the sequence without structural deformation. This performance delta makes the sequence an ideal benchmark for comparing classical robustness against deep-learning resilience.