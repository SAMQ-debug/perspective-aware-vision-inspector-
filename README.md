# perspective-aware-vision-inspector-
Perspective-Aware Vision Inspector is a classical Computer Vision system for feature-based image analysis and perspective correction. It combines image preprocessing, gradient and edge detection, Harris corners, Hough lines, ORB feature extraction, descriptor matching, RANSAC-based homography estimation
# Perspective-Aware Vision Inspector

## What this project does

This project is a classical Computer Vision system built around a practical question:

**scene → smoothing → gradients → edges → geometric features → local feature matching → RANSAC homography → rectification**

The implementation is command-line based and produces inspectable intermediate results.

## Main Computer Vision concepts

- Grayscale conversion
- Gaussian filtering
- Sobel gradient magnitude
- Canny edge detection
- Harris corner detection
- Probabilistic Hough lines
- Contour extraction
- ORB local features
- Hamming-distance matching
- Lowe-style ratio filtering
- RANSAC homography
- Perspective rectification

## Requirements

Python 3.9+ is recommended.

Install dependencies:

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

Linux/macOS:

```bash
source .venv/bin/activate
```

Then:

```bash
pip install -r requirements.txt
```

## Run the supplied experiment

From the repository root:

```bash
python src/vision_inspector.py --scene data/campus_scene.jpg --reference data/reference_board.jpg --out results
```

Optional edge thresholds:

```bash
python src/vision_inspector.py --scene data/campus_scene.jpg --reference data/reference_board.jpg --out results --low 45 --high 135
```

## Generated results

The `results` directory contains:

1. `01_gray.png` — grayscale scene
2. `02_smoothed.png` — Gaussian-smoothed scene
3. `03_gradient_energy.png` — Sobel gradient magnitude
4. `04_edges.png` — Canny edge map
5. `05_harris_corners.png` — Harris corner candidates
6. `06_hough_lines.png` — Hough line segments
7. `07_contours.png` — external contours
8. `08_ransac_projection.png` — reference boundary projected into the scene
9. `09_orb_matches.png` — accepted ORB correspondences
10. `10_rectified_scene.png` — scene warped back toward the reference viewpoint
11. `summary.txt` — numerical experiment results

## Why RANSAC is used

Feature matching can contain incorrect correspondences. A homography estimated from every match would therefore be unreliable. RANSAC repeatedly considers subsets of correspondences and selects a transformation supported by a consistent group of points. The final inlier count provides a simple quantitative measure of geometric consistency.



## Reproducibility

The repository includes its own reference and scene images, so a fresh clone can execute the same experiment without obtaining external data.

## Important interpretation note

A high number of ORB matches alone does not prove that the object was correctly located. The RANSAC inlier count and the visual position of the projected quadrilateral should be considered together.

