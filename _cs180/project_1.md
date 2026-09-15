---
title: "Project 1: Images of the Russian Empire: Colorizing the Prokudin-Gorskii Photo Collection"
collection: cs180
permalink: /cs180/project_1/
date: 2026-09-14
layout: projects
---

<script>
window.MathJax = {
  tex: {
    inlineMath: [['$', '$']],
    displayMath: [['$$', '$$']]
  }
};
</script>
<script id="MathJax-script" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js"></script>

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

> **One thing left to fix before this page is done:** every full-resolution image (everything except `cathedral`, `monastery`, `tobolsk`) was saved with its original `.tif` extension, and Chrome, Firefox, and Edge don't render TIFF in an `<img>` tag — those panels will show as broken images for basically all your visitors even though the paths below are correct. Batch-convert those outputs to `.jpg` (script below) and re-upload, then send me the new file listing and I'll swap the extensions in one pass.

Single-scale, brute-force alignment (window $[-15,15]$, metric computed on the middle 80% of each channel) run on the three low-resolution `.jpg` plates:

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/cathedral_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/cathedral_proc_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (5, 2)<br>R: (12, 3)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/cathedral_proc_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (5, 2)<br>R: (12, 3)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Cathedral</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/monastery_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/monastery_proc_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (-3, 2)<br>R: (3, 2)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/monastery_proc_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (-3, 2)<br>R: (3, 2)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Monastery</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/tobolsk_no_align.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/tobolsk_proc_l2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (3, 3)<br>R: (6, 3)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/tobolsk_proc_ncc.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (3, 3)<br>R: (6, 3)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Tobolsk</p>

## Part 2: Multi-Scale Pyramid Alignment Results

Pyramid alignment (per-level search windows of $[-15,16)$ at the coarsest scale and $[-3,4)$ at every finer scale) run on all 14 provided glass plates plus 3 additional plates chosen from the [Prokudin-Gorskii collection](https://www.loc.gov/collections/prokudin-gorskii/?st=grid). `cathedral`, `monastery`, and `tobolsk` are shown above in Part 1 — the pyramid algorithm converges to the same shifts on these since they're already low-resolution; the 11 full-size `.tif` scans below only became tractable with the pyramid.

> **Note on the numbers below:** the L2- and NCC-alignment shifts currently come out identical for every image. That's an artifact of how `align()` is wired up right now — it always scores candidate shifts with `l2()`, regardless of which output folder the result gets saved to — not a coincidence in the data. The images/shifts below are accurate for the L2 metric; to get genuine NCC numbers, `align()` needs a metric argument that actually dispatches to `ncc()` on the second pass.

### Provided Images

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/church_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/church_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (25, 4)<br>R: (58, -4)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/church_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (25, 4)<br>R: (58, -4)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Church</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/emir_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/emir_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (49, 24)<br>R: (95, -249)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/emir_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (49, 24)<br>R: (95, -249)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Emir of Bukhara — see failure analysis below</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/harvesters_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/harvesters_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (59, 16)<br>R: (123, 13)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/harvesters_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (59, 16)<br>R: (123, 13)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Harvesters</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/icon_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/icon_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (41, 17)<br>R: (89, 23)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/icon_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (41, 17)<br>R: (89, 23)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Icon</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/ilemselga_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/ilemselga_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (40, 7)<br>R: (130, 11)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/ilemselga_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (40, 7)<br>R: (130, 11)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Ilemselga</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/melons_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/melons_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (81, 10)<br>R: (178, 13)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/melons_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (81, 10)<br>R: (178, 13)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Melons</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/religous_painting_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/religous_painting_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (27, 3)<br>R: (68, 7)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/religous_painting_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (27, 3)<br>R: (68, 7)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Religious Painting</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/self_portrait_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/self_portrait_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (78, 29)<br>R: (176, 37)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/self_portrait_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (78, 29)<br>R: (176, 37)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Self Portrait</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/siren_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/siren_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (49, -6)<br>R: (95, -25)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/siren_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (49, -6)<br>R: (95, -25)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Siren</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/three_generations_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/three_generations_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (53, 14)<br>R: (112, 11)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/three_generations_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (53, 14)<br>R: (112, 11)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Three Generations</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/wharf_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_given/wharf_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (15, -7)<br>R: (82, -16)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_given/wharf_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (15, -7)<br>R: (82, -16)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Wharf</p>

### My Own Selections

Three additional plates chosen from the LoC's online Prokudin-Gorskii collection:

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/bridge_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_additional/bridge_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (35, 0)<br>R: (124, -1)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_additonal/bridge_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (35, 0)<br>R: (124, -1)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Bridge</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/building1_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_additional/building1_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (32, -16)<br>R: (78, -25)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_additonal/building1_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (32, -16)<br>R: (78, -25)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Building</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/no_align/lake_building_no_align.tif" style="width: 100%;">
    <figcaption style="text-align: center;">No Alignment</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/l2_additional/lake_building_proc_l2.tif" style="width: 100%;">
    <figcaption style="text-align: center;">L2 Alignment<br>G: (41, -16)<br>R: (92, -29)</figcaption>
  </figure>
  <figure style="width: 33%; margin: 0;">
    <img src="/images/cs180/proj1/ncc_additonal/lake_building_proc_ncc.tif" style="width: 100%;">
    <figcaption style="text-align: center;">NCC Alignment<br>G: (41, -16)<br>R: (92, -29)</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Lake Building</p>

### Offsets Summary

| Image | L2: G (dy, dx) | L2: R (dy, dx) | NCC: G (dy, dx) | NCC: R (dy, dx) | Notes |
|---|---|---|---|---|---|
| cathedral | (5, 2) | (12, 3) | (5, 2) | (12, 3) | |
| monastery | (-3, 2) | (3, 2) | (-3, 2) | (3, 2) | |
| tobolsk | (3, 3) | (6, 3) | (3, 3) | (6, 3) | |
| church | (25, 4) | (58, -4) | (25, 4) | (58, -4) | |
| emir | (49, 24) | (95, -249) | (49, 24) | (95, -249) | likely failure — see below |
| harvesters | (59, 16) | (123, 13) | (59, 16) | (123, 13) | |
| icon | (41, 17) | (89, 23) | (41, 17) | (89, 23) | |
| ilemselga | (40, 7) | (130, 11) | (40, 7) | (130, 11) | |
| melons | (81, 10) | (178, 13) | (81, 10) | (178, 13) | |
| religous_painting | (27, 3) | (68, 7) | (27, 3) | (68, 7) | |
| self_portrait | (78, 29) | (176, 37) | (78, 29) | (176, 37) | |
| siren | (49, -6) | (95, -25) | (49, -6) | (95, -25) | |
| three_generations | (53, 14) | (112, 11) | (53, 14) | (112, 11) | |
| wharf | (15, -7) | (82, -16) | (15, -7) | (82, -16) | |
| bridge (mine) | (35, 0) | (124, -1) | (35, 0) | (124, -1) | |
| building1 (mine) | (32, -16) | (78, -25) | (32, -16) | (78, -25) | |
| lake_building (mine) | (41, -16) | (92, -29) | (41, -16) | (92, -29) | |

## Failure Case: The Emir of Bukhara

<div style="text-align: center; margin-bottom: 6px;">
  <img src="/images/cs180/proj1/no_align/emir_no_align.tif" style="width: 60%;">
</div>

The Emir of Bukhara is the standard example of an image where simple pixel-based alignment struggles, and it's worth explaining *why* rather than just noting that it fails. The Emir is photographed wearing an elaborately patterned robe that is strongly blue. Because blue dominates so much of the frame, the blue-channel exposure of the robe looks very different in brightness and texture from how the same robe appears in the green and red exposures — the whole premise of L2 and NCC is that corresponding regions should have *similar* pixel values (L2) or vary *together* around their mean (NCC) across channels, and a region that is bright in one channel's filter response but comparatively flat or dark in another's breaks that assumption. The metric ends up chasing a shift that best matches the robe's high-contrast folds against unrelated structure elsewhere in the frame, rather than truly registering the three exposures, so the alignment search can converge on the wrong displacement. This is exactly the scenario the assignment calls out: the two channels being compared don't actually share the same brightness statistics, so a smarter metric or feature representation (e.g., aligning on gradients/edges instead of raw intensities) is needed to do better here.

The computed shift backs this up: red comes out at $(dy, dx) = (95, -249)$ — an *x*-displacement roughly 10–20× larger in magnitude than any other image in the set. That's a strong sign the search converged on a spurious match rather than the true registration, which makes Emir the most likely candidate for the one alignment failure the rubric allows. TODO: swap in the colorized output image above and confirm visually that it does in fact show a misaligned result (and that no other image in the set failed instead).

## Bells & Whistles

*(Optional for CS180 — required for CS280A.)* Not yet implemented in this submission. Natural next steps, in rough order of expected payoff: automatic border cropping (detect the misaligned/colored edge rather than cropping a fixed percentage), automatic contrast stretching, and gray-world automatic white balance — all of which the assignment page describes in more detail.

## Mistakes and Detours

- The first version of the pyramid search ran far too slowly. The fix was to only brute-force the full $[-15,16)$ window at the coarsest ($\tfrac{1}{16}$-scale) level, and only search a small $[-3,4)$ refinement window at every finer level, since the coarse level already gets the shift within a couple of pixels.
- Switched from `scipy`'s sub-pixel `shift()` to `np.roll` for applying integer-pixel translations — since the searched displacements are always whole pixels, `np.roll` gives the same result for a fraction of the runtime.

## Reflection

Working through this project, what stuck with me most wasn't the alignment math so much as the photographs themselves. History classes tend to focus on the big events and the famous names, but these glass plates are full of ordinary people — merchants, laborers, families — going about their lives in the last years of Imperial Russia, a century before I was born. Watching a flat, misaligned scan resolve into a coherent color photograph feels a little like watching that history come back into focus.