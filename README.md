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
* **Privacy & Synthetic Anchoring:** To comply with privacy standards, faces in this sequence are anonymized. Rather than using standard algorithmic masking, redactions are applied as mathematically rigid, axis-aligned squares using an exceptionally heavy Gaussian blur (a `99x99` kernel). This aggressive macro-blur obliterates internal gradients but creates sharp 90-degree boundary corners. These corners inject high-contrast synthetic keypoints into the scene, acting as geometric anchors that inadvertently help prevent classical algorithms (like SIFT) from tracking failure in the feature-starved, highly symmetrical environment. Forcing a strict 1:1 square geometry eliminates aspect-ratio jitter across frames. This mathematical consistency ensures the synthetic corners do not stretch or morph in 3D space, preventing violations of the solver's rigid scene assumption that would otherwise exacerbate scale drift.
* **Known Baseline Behaviors:** This is an aggressively difficult sequence designed to stress-test feature extraction and geometric solver resilience. Because algorithms inadvertently extract features from the 2D axis-aligned synthetic anchors alongside the natural architecture, the redactions introduce inherent violations of 3D epipolar geometry during camera roll. Consequently, it is expected that many feature matchers will exhibit minor relative scale drift in these localized segments as the PnP solver compromises to reconcile the geometric paradox. Furthermore, the extreme architectural symmetry exposes clear performance deltas across different extraction architectures, particularly in their ability to prevent feature starvation and yield dense, complete structural outlines rather than sparser reconstructions. These characteristics make the dataset a practical benchmark for evaluating how various algorithms respond to visually repetitive, mathematically hostile conditions.