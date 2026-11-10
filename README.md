# incremental-sfm-datasets

This repository contains multi-view image sequence datasets captured across the Nanyang Technological University (NTU) campus, specifically curated for evaluating and benchmarking Structure from Motion (SfM) pipelines under unconstrained real-world conditions.

## Companion Repositories

This data repository is part of a broader project suite for 3D computer vision evaluation:

- **[`incremental-sfm`](https://github.com/chuo-v/incremental-sfm):** The core pure-Python incremental SfM engine and modular feature-matching pipeline (`SIFT`, `SuperPoint` + `LightGlue`, and `ORB`).
- **[`incremental-sfm-experiments`](https://github.com/chuo-v/incremental-sfm-experiments):** The analytical workspace containing evaluation notebooks, ablation studies, metric logs, and interactive 3D HTML visualizations.

## Repository Structure

Each dataset folder follows the standard directory contract required by the `incremental-sfm` data loader:

```text
incremental-sfm-datasets/
├── nantah-memorial-P42/
│   ├── images/                 # 42 sequential frames (0000.jpg to 0041.jpg)
│   └── K.txt                   # 3x3 space-separated camera intrinsic matrix
├── yunnan-garden-pavilion-P71/
│   ├── images/                 # 71 sequential frames (0000.jpg to 0070.jpg)
│   └── K.txt                   # 3x3 space-separated camera intrinsic matrix
├── README.md                   # Project documentation
└── LICENSE                     # CC0-1.0 license
```

## Datasets Overview

| Dataset Folder | Frames | Resolution ($W \times H$) | Trajectory Type | Primary SfM Challenges |
| --- | --- | --- | --- | --- |
| [`nantah-memorial-P42`](./nantah-memorial-P42/) | 42 (`0000.jpg`–`0041.jpg`) | $3072 \times 4080\text{ px}$ | Multi-View Orbital Sequence | Narrow central axis, uniform concrete facades, repeating roof motifs, foreground branch occlusions |
| [`yunnan-garden-pavilion-P71`](./yunnan-garden-pavilion-P71/) | 71 (`0000.jpg`–`0070.jpg`) | $3072 \times 4080\text{ px}$ | Multi-View Orbital Sequence | Extreme 8-fold rotational symmetry, dense background foliage, stationary rotational pans, synthetic privacy anchors |

### 1. Nantah Memorial ([`nantah-memorial-P42`](./nantah-memorial-P42/))

- **The Subject:** The historic Nantah Memorial, a tall, narrow reinforced concrete monument designed in the Chinese National style, featuring an octagonal base and a traditional double-tiered roof with glazed tiles.
- **SfM Challenge:** Evaluates geometric tracking around a narrow central axis where dense orbital camera steps create shallow parallax angles. The dataset tests the pipeline's ability to filter out dynamic occlusion from foreground branches and swaying leaves while maintaining a rigid lock on the underlying concrete structure and roof motifs.

### 2. Yunnan Garden Pavilion ([`yunnan-garden-pavilion-P71`](./yunnan-garden-pavilion-P71/))

- **The Subject:** A traditional octagonal pavilion featuring highly symmetric architecture, identical red pillars, and repeating lattice patterns.
- **SfM Challenge:** Specifically designed to test a feature matcher's ability to resolve extreme architectural symmetry and severe depth parallax without starving the sequential tracker, while simultaneously filtering out heavy background foliage.
- **Privacy & Synthetic Anchoring:** To comply with privacy standards, faces in this sequence are anonymized. Rather than using standard algorithmic masking, redactions are applied as mathematically rigid, axis-aligned squares using an exceptionally heavy Gaussian blur (a `99x99` kernel). This aggressive macro-blur obliterates internal gradients but creates sharp 90-degree boundary corners. These corners inject high-contrast synthetic keypoints into the scene, acting as geometric anchors that inadvertently help prevent classical algorithms (like SIFT) from tracking failure in the feature-starved, highly symmetrical environment. Forcing a strict 1:1 square geometry eliminates aspect-ratio jitter across frames. This mathematical consistency ensures the synthetic corners do not stretch or morph in 3D space, preventing violations of the solver's rigid scene assumption that would otherwise exacerbate scale drift.
- **Known Baseline Behaviors:** This is an aggressively difficult sequence designed to stress-test feature extraction and geometric solver resilience. Because algorithms inadvertently extract features from the 2D axis-aligned synthetic anchors alongside the natural architecture, the redactions introduce inherent violations of 3D epipolar geometry during camera roll. Consequently, it is expected that many feature matchers will exhibit minor relative scale drift in these localized segments as the PnP solver compromises to reconcile the geometric paradox. Furthermore, the extreme architectural symmetry exposes clear performance deltas across different extraction architectures, particularly in their ability to prevent feature starvation and yield dense, complete structural outlines rather than sparser reconstructions. These characteristics make the dataset a practical benchmark for evaluating how various algorithms respond to visually repetitive, mathematically hostile conditions.

## Camera Intrinsic Calibration (`K.txt`)

Both sequences were captured at native portrait resolution ($W \times H = 3072 \times 4080\text{ px}$) with locked optical zoom ($f_{35\text{mm}} = 24\text{ mm}$ equivalent focal length across a standard $36\text{ mm}$ full-frame sensor width). Following standard unconstrained SfM initialization conventions, the base pinhole intrinsic matrix $K$ is derived from EXIF metadata assuming a centered principal point ($c_x, c_y$) and zero skew:

$$f_{\text{px}} = \frac{4080 \times 24\text{ mm}}{36\text{ mm}} = 2720.0\text{ px}, \quad c_x = \frac{3072}{2} = 1536.0\text{ px}, \quad c_y = \frac{4080}{2} = 2040.0\text{ px}$$

Each dataset's `K.txt` file stores this $3 \times 3$ matrix in space-separated format:

```text
2720.0 0.0 1536.0
0.0 2720.0 2040.0
0.0 0.0 1.0
```

## Usage

### Option 1: Clone the Full Dataset Repository

Clone this repository and point the `DATA_DIR` environment variable in your [`incremental-sfm`](https://github.com/chuo-v/incremental-sfm) `.env` file to the cloned path:

```bash
git clone https://github.com/chuo-v/incremental-sfm-datasets.git
```

### Option 2: Download a Single Dataset via Sparse Checkout

To save disk space or bandwidth (e.g., in cloud notebook environments), you can fetch an individual sequence using Git sparse checkout:

```bash
git clone --depth 1 --filter=blob:none --sparse https://github.com/chuo-v/incremental-sfm-datasets.git
cd incremental-sfm-datasets
git sparse-checkout set nantah-memorial-P42
```