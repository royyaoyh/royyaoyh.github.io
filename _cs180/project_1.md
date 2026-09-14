---
title: "Project 1: Images of the Russian Empire: Colorizing the Prokudin-Gorskii Photo Collection"
collection: cs180
permalink: /cs180/project_1/
date: 2026-09-14
layout: projects
---

## Overview

Between 1907 and 1915, the Russian photographer Sergei Mikhailovich Prokudin-Gorskii traveled across the Russian Empire under a special permit from Tsar Nicholas II, photographing everything from cathedrals and railroads to portraits of ordinary people — including the only known color portrait of Leo Tolstoy. Since color film did not exist yet, he captured each scene three times through red, green, and blue filters onto a single glass plate, planning to project all three together later. He never got the chance: he left Russia after the 1917 revolution and never returned. His glass plates survived, however, and were digitized by the Library of Congress.

The goal of this project is to take a digitized glass plate scan — a single tall grayscale image stacked as blue, green, and red exposures from top to bottom — and automatically reconstruct it as an aligned color photograph. Because the three exposures were taken sequentially rather than simultaneously, the camera (or the subject) shifted slightly between shots, so the channels need to be translated back into registration before they can be stacked into an RGB image.

## Approach

### Splitting the Plate

Each input image is loaded as grayscale and normalized to floating point in $[0,1]$. Since the three exposures are stacked in equal thirds (B on top, then G, then R), the image height is divided by three and sliced into the blue, green, and red channels. Blue is treated as the fixed reference channel; green and red are each translated to align with it.

### Alignment Metrics

Two metrics are used to score how well a candidate shift aligns two channels:

**L2 norm (Euclidean distance)** — the sum of squared pixel differences between the two channels:

$$
L_2(A, B) = \left\| A - B \right\|_2 = \sqrt{\sum_{i,j} \left(A_{i,j} - B_{i,j}\right)^2}
$$

Lower is better: a shift of zero error means the two channels are pixel-identical.

**Normalized cross-correlation (NCC)** — the dot product of the two channels after each is mean-subtracted and normalized to unit length:

$$
\hat{A} = \frac{A - \bar{A}}{\left\| A - \bar{A} \right\|_2}, \qquad
\hat{B} = \frac{B - \bar{B}}{\left\| B - \bar{B} \right\|_2}
$$

$$
NCC(A, B) = \hat{A} \cdot \hat{B} = \sum_{i,j} \hat{A}_{i,j}\, \hat{B}_{i,j}
$$

Higher is better here: a value close to $1$ means the two channels vary together almost perfectly. Where L2 penalizes raw brightness differences, NCC only cares about whether the two images vary *in the same direction* from their own mean — which makes it more forgiving of small exposure differences between channels, but (as discussed below) not immune to them.

To keep both metrics from being dominated by the mismatched, low-information borders of the scanned plate, the metric is only computed on the middle 80% of each channel (10% cropped from every edge) before scoring a candidate shift.

### Single-Scale (Brute-Force) Alignment

The simplest way to find the best shift is exhaustive search: for every candidate displacement $(dx, dy)$ in a window, roll the comparison channel by that amount with `np.roll` and score it against the base channel, keeping whichever shift scores best. For the small `.jpg` plates (roughly a few hundred pixels tall), a window of $[-15, 15]$ in both $x$ and $y$ — 961 combinations — is cheap enough to brute-force directly and is used for the single-scale results below.

### Multi-Scale (Pyramid) Alignment

Brute-force search over $[-15,15]$ becomes far too slow once displacements can be tens or hundreds of pixels, which is the case for the full-resolution `.tif` scans. Instead, an image pyramid is built for each channel: the image is repeatedly blurred and downsampled by a factor of 2, four times, producing versions at $\tfrac12, \tfrac14, \tfrac18, \tfrac{1}{16}$ of the original resolution. Blurring before downsampling (rather than simply subsampling) avoids aliasing.

The blur uses a separable binomial kernel, the outer product of $\begin{bmatrix}1 & 4 & 6 & 4 & 1\end{bmatrix}/16$ with itself — a discrete approximation of a Gaussian:

$$
K = \frac{1}{16}\begin{bmatrix}1\\4\\6\\4\\1\end{bmatrix}
\frac{1}{16}\begin{bmatrix}1 & 4 & 6 & 4 & 1\end{bmatrix}
=
\frac{1}{256}
\begin{bmatrix}
1 & 4 & 6 & 4 & 1\\
4 & 16 & 24 & 16 & 4\\
6 & 24 & 36 & 24 & 6\\
4 & 16 & 24 & 16 & 4\\
1 & 4 & 6 & 4 & 1
\end{bmatrix}
$$

Alignment then proceeds coarse-to-fine: starting at the smallest ($\tfrac{1}{16}$-scale) image, a wide search of $[-15, 16)$ is run in both axes to find a rough shift, since at this resolution even a large true displacement only spans a handful of pixels. That estimate is then doubled and carried down to the next-finer level as a starting offset, where only a small refinement search of $[-3, 4)$ is needed to correct for rounding and quantization from the coarser scale. This doubling-and-refining repeats down to the full-resolution image, so the total search cost stays roughly constant per level instead of growing with image size. Shifts are applied with `np.roll` rather than a fractional/interpolated shift, since it is exact for integer-pixel translations and much faster.

Because the collection's plates are a consistent size, the pyramid depth (4 downsampling steps) is hardcoded rather than computed dynamically from the input size.

## Part 1: Single-Scale Alignment Results

Single-scale, brute-force alignment (window $[-15,15]$, metric computed on the middle 80% of each channel) run on the three low-resolution `.jpg` plates:

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/cathedral_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/cathedral_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/cathedral_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Cathedral</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/monastery_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/monastery_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/monastery_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Monastery</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/tobolsk_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/tobolsk_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/tobolsk_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Tobolsk</p>

## Part 2: Multi-Scale Pyramid Alignment Results

Pyramid alignment (per-level search windows of $[-15,16)$ at the coarsest scale and $[-3,4)$ at every finer scale) run on all 14 provided glass plates plus 3 additional plates chosen from the [Prokudin-Gorskii collection](https://www.loc.gov/collections/prokudin-gorskii/?st=grid).

<!--
  Repeat the block below once per image — 14 provided + 3 of your own — swapping in the
  actual filename and the (dy, dx) shifts your script prints for that image. The block
  below is filled in as a worked example using main.py's own default image.
-->

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/church_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/church_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/church_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (dy, dx) = TODO<br>R: (dy, dx) = TODO</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Church</p>

<!-- [duplicate the block above for the remaining 13 provided images] -->

### My Own Selections

<!-- [duplicate the same block above for each of your 3 self-selected images from the LoC collection, using the l2_additional / ncc_additional folders] -->

### Offsets Summary

| Image | L2: G (dy, dx) | L2: R (dy, dx) | NCC: G (dy, dx) | NCC: R (dy, dx) | Notes |
|---|---|---|---|---|---|
| cathedral | TODO | TODO | TODO | TODO | |
| monastery | TODO | TODO | TODO | TODO | |
| tobolsk | TODO | TODO | TODO | TODO | |
| church | TODO | TODO | TODO | TODO | |
| ... | | | | | *(one row per remaining provided image)* |
| *(your selection 1)* | TODO | TODO | TODO | TODO | |
| *(your selection 2)* | TODO | TODO | TODO | TODO | |
| *(your selection 3)* | TODO | TODO | TODO | TODO | |

## Failure Case: The Emir of Bukhara

<div style="text-align: center; margin-bottom: 6px;">
  <img src="/images/cs180/proj1/no_align/emir_no_align.jpg" style="width: 60%;">
</div>

The Emir of Bukhara is the standard example of an image where simple pixel-based alignment struggles, and it's worth explaining *why* rather than just noting that it fails. The Emir is photographed wearing an elaborately patterned robe that is strongly blue. Because blue dominates so much of the frame, the blue-channel exposure of the robe looks very different in brightness and texture from how the same robe appears in the green and red exposures — the whole premise of L2 and NCC is that corresponding regions should have *similar* pixel values (L2) or vary *together* around their mean (NCC) across channels, and a region that is bright in one channel's filter response but comparatively flat or dark in another's breaks that assumption. The metric ends up chasing a shift that best matches the robe's high-contrast folds against unrelated structure elsewhere in the frame, rather than truly registering the three exposures, so the alignment search can converge on the wrong displacement. This is exactly the scenario the assignment calls out: the two channels being compared don't actually share the same brightness statistics, so a smarter metric or feature representation (e.g., aligning on gradients/edges instead of raw intensities) is needed to do better here.

TODO: once you've run the pipeline, report whether Emir was in fact the one allowed failure, or whether a different image failed instead — and swap this explanation to match what you actually observed.

## Bells & Whistles

*(Optional for CS180 — required for CS280A.)* Not yet implemented in this submission. Natural next steps, in rough order of expected payoff: automatic border cropping (detect the misaligned/colored edge rather than cropping a fixed percentage), automatic contrast stretching, and gray-world automatic white balance — all of which the assignment page describes in more detail.

## Mistakes and Detours

- The first version of the pyramid search ran far too slowly. The fix was to only brute-force the full $[-15,16)$ window at the coarsest ($\tfrac{1}{16}$-scale) level, and only search a small $[-3,4)$ refinement window at every finer level, since the coarse level already gets the shift within a couple of pixels.
- Switched from `scipy`'s sub-pixel `shift()` to `np.roll` for applying integer-pixel translations — since the searched displacements are always whole pixels, `np.roll` gives the same result for a fraction of the runtime.

## Reflection

Working through this project, what stuck with me most wasn't the alignment math so much as the photographs themselves. History classes tend to focus on the big events and the famous names, but these glass plates are full of ordinary people — merchants, laborers, families — going about their lives in the last years of Imperial Russia, a century before I was born. Watching a flat, misaligned scan resolve into a coherent color photograph feels a little like watching that history come back into focus.
