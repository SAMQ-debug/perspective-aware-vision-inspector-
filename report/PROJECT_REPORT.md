# PROJECT REPORT

## Title

**Perspective-Aware Vision Inspector: Classical Feature Extraction and Robust Homography Estimation**

## 1. Abstract

This project implements a command-line Computer Vision pipeline for analysing a planar visual object inside a perspective-distorted scene. The system combines low-level image processing with local feature matching and robust geometric estimation. Gaussian smoothing and Sobel gradients provide an initial representation of image structure. Canny edges, Harris corners, Hough lines and contours expose geometric information. ORB descriptors are then matched between a reference image and the scene. A ratio test removes weak correspondences, after which RANSAC estimates a homography while rejecting inconsistent matches. The resulting transformation is used to project the reference boundary and to rectify the detected scene region.

## 2. Problem Statement

A reference object may appear rotated, scaled or perspectively distorted when photographed in a real environment. A Computer Vision system should therefore avoid relying only on raw pixel similarity. This project investigates a feature-based approach in which local image structures are matched and then validated using a geometric model.

## 3. Objectives

- Build a reproducible command-line Computer Vision application.
- Demonstrate fundamental image-processing operations.
- Extract edges, corners, lines and contours.
- Detect local ORB features.
- Match binary descriptors.
- Reject weak feature correspondences.
- Estimate a homography with RANSAC.
- Project the reference boundary into the scene.
- Rectify the scene using the estimated transformation.
- Report quantitative measurements.

## 4. Dataset

Two locally generated images are included:

- `data/reference_board.jpg`: frontal view of a synthetic planar board.
- `data/campus_scene.jpg`: the same board inserted into a perspective-distorted scene.

The use of local sample data makes the experiment reproducible after cloning the repository.

## 5. Pipeline

```text
Reference image ──> ORB ──> descriptors ──┐
                                          ├─> Hamming matching
Scene image ───────> ORB ──> descriptors ─┘
                                          |
                                     Ratio filtering
                                          |
                                          v
                                     RANSAC
                                          |
                              Homography matrix H
                                          |
                         +----------------+----------------+
                         |                                 |
                         v                                 v
                  Boundary projection                Rectification

Scene image
   |
   v
Grayscale
   |
   v
Gaussian smoothing
   |
   +--> Sobel gradient magnitude
   |
   +--> Canny edges --> Hough lines
   |                └-> contours
   |
   └--> Harris corners
```

## 6. Algorithm Description

### 6.1 Gaussian filtering

The grayscale image is convolved with a Gaussian kernel. This suppresses small image fluctuations before edge detection.

### 6.2 Sobel gradient

Horizontal and vertical derivatives are calculated. Their Euclidean magnitude gives an estimate of local gradient energy.

### 6.3 Canny

Canny transforms the smoothed image into a binary edge map. The two thresholds are exposed as command-line parameters so the experiment can be repeated under different settings.

### 6.4 Harris

The Harris response measures changes in intensity in multiple directions. Pixels above a response threshold are marked as corner candidates.

### 6.5 Hough lines

The probabilistic Hough transform identifies groups of edge points that form approximately straight segments.

### 6.6 Contours

External connected boundaries are extracted from the Canny result.

### 6.7 ORB

ORB detects local keypoints and creates binary descriptors. Binary descriptors can be compared efficiently using Hamming distance.

### 6.8 Ratio filtering

Each descriptor is compared with its two nearest scene descriptors. A correspondence is retained only when its best distance is sufficiently smaller than the second-best distance.

### 6.9 RANSAC homography

A homography maps points on one planar view to another:

p' ~ H p

where H is a 3 × 3 projective transformation matrix. RANSAC estimates H while limiting the influence of incorrect correspondences.

## 7. Quantitative Measures

The program records:

### Edge density

Edge density is defined as:

**number of non-zero edge pixels / total number of pixels**

It provides a simple measure of how much of the image is occupied by detected boundaries.

### Mean line length

For every detected Hough line segment:

**L = sqrt((x2 − x1)^2 + (y2 − y1)^2)**

The mean of these lengths is reported.

### RANSAC inlier ratio

**Inlier ratio = RANSAC inliers / ratio-test matches**

This indicates what fraction of accepted matches agrees with the estimated geometric model.

## 8. Experimental Procedure

1. Create the Python environment.
2. Install the dependencies.
3. Run the supplied scene/reference experiment.
4. Inspect every generated image.
5. Record the values from `summary.txt`.
6. Repeat with different Canny thresholds if required.
7. Compare the projected boundary with the actual object location.
8. Examine the ORB match image for incorrect correspondences.
9. Use the rectified output to assess the estimated homography.

## 9. Results Table

Fill the following values using your own execution:

| Measure | Observed result |
|---|---:|
| Scene width | |
| Scene height | |
| Edge density | |
| Harris corner candidates | |
| Hough line segments | |
| Mean line length | |
| External contours | |
| Largest contour area | |
| Reference ORB keypoints | |
| Ratio-test matches | |
| RANSAC inliers | |
| RANSAC inlier ratio | |

## 10. Result Discussion

The following points should be discussed after running the project:

### Edge representation

Describe whether the major board boundaries and internal structures are visible in `04_edges.png`.

### Corners

Inspect `05_harris_corners.png` and identify whether corners occur around strong geometric intersections.

### Lines

Compare the Hough detections with the straight boundaries visible in the original scene.

### Contours

Discuss whether the extracted contours correspond to meaningful object boundaries or include noise.

### Feature matching

Inspect `09_orb_matches.png`. Distinguish visually consistent correspondences from isolated incorrect matches.

### Homography

Inspect `08_ransac_projection.png`. Explain whether the projected reference boundary follows the perspective-distorted board.

### Rectification

Inspect `10_rectified_scene.png` and explain whether the planar object has been transformed toward its reference viewpoint.

## 11. Limitations

- Fixed detector parameters are not optimal for every image.
- Low-texture reference objects may produce too few ORB features.
- Strong blur or severe illumination changes can reduce descriptor matching quality.
- Hough detection may produce false positives.
- A homography is appropriate for planar scenes or approximately planar surfaces; it is not a general 3D transformation.
- RANSAC results depend on the quality and distribution of the candidate correspondences.

## 12. Future Enhancements

- Automatic threshold selection.
- Multi-scale comparison.
- Comparison of ORB with another feature detector.
- Video-stream processing.
- Camera calibration.
- Fundamental/essential matrix estimation for non-planar scenes.
- Quantitative evaluation against manually labelled correspondences.
- Real-time performance profiling.

## 13. Conclusion

The project demonstrates how several classical Computer Vision operations can be integrated into one practical workflow. Low-level processing exposes image structure, while ORB matching and RANSAC provide a higher-level geometric verification stage. The final system remains lightweight, reproducible and executable entirely from a terminal.
