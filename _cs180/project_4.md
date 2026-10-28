---
title: "Project 4A: Image Warping and Mosaicing"
collection: cs180
permalink: /cs180/project_4/
date: 2026-10-28
layout: projects
---

<script>
window.MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']],
    displayMath: [['$$', '$$'], ['\\[', '\\]']]
  }
};
</script>
<script id="MathJax-script" async src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js"></script>

<style>
/* ---- Project 3 page styles (scoped with p3- prefixes) ---- */
.page__title, h1.page__title, article h1, .page__content h1 { font-size: 2.4em !important; }
.page__content h2, article h2 { font-size: 1.8em !important; }
.page__content h3, article h3 { font-size: 1.45em !important; margin-top: 2em; }
.page__content h4, article h4 { font-size: 1.2em !important; margin-top: 1.6em; }
.page__content h5, article h5 { font-size: 1.05em !important; margin-top: 1.4em; text-transform: none !important; letter-spacing: normal !important; }
.p3-grid { --n: 3; --g: 14px; display: flex; flex-wrap: wrap; justify-content: center; align-items: flex-start; gap: var(--g); margin: 1.4em auto 0.6em; }
.p3-grid figure { display: block; margin: 0; min-width: 0; text-align: center; flex: 0 0 calc((100% - (var(--n) - 1) * var(--g)) / var(--n)); }
.p3-grid figure.w2 { flex-basis: calc(2 * (100% - (var(--n) - 1) * var(--g)) / var(--n) + var(--g)); }
.p3-grid img { display: block; width: 100%; height: auto; margin: 0; border-radius: 3px; }
.p3-grid figcaption { margin: 0.45em 0 0; font-size: 0.85em; font-style: italic; line-height: 1.35; color: inherit; opacity: 0.85; text-align: center; }
.p3-grid.c1 { --n: 1; }
.p3-grid.c2 { --n: 2; }
.p3-grid.c3 { --n: 3; }
.p3-grid.c4 { --n: 4; }
.p3-grid.narrow { max-width: 640px; }
.p3-grid.medium { max-width: 820px; }
.p3-cap { text-align: center; font-style: italic; font-size: 0.9em; color: inherit; opacity: 0.85; margin: 0.2em 0 1.6em; }
.p3-table { width: auto !important; display: table !important; margin: 1em auto 1.4em !important; border-collapse: collapse; }
.p3-table th, .p3-table td { text-align: center !important; padding: 0.45em 1.8em; white-space: nowrap; }
.p3-note { color: inherit; border-left: 4px solid #6f97c9; background: rgba(111, 151, 201, 0.14); padding: 0.7em 1.1em; margin: 1.4em 0; border-radius: 0 4px 4px 0; }
.p3-note p:last-child, .p3-note ol:last-child { margin-bottom: 0; }
.p3-note ol { margin-top: 0.4em; }
.p3-cmp { display: block !important; max-width: 640px; margin: 1.4em auto 0.6em !important; text-align: center; }
.p3-cmp-stage { position: relative; overflow: hidden; line-height: 0; border-radius: 4px; touch-action: pan-y; -webkit-user-select: none; user-select: none; cursor: ew-resize; }
.p3-cmp-stage img { display: block; width: 100%; height: auto; margin: 0 !important; pointer-events: none; -webkit-user-drag: none; }
.p3-cmp-stage img.p3-layer { position: absolute; top: 0; left: 0; height: 100%; object-fit: cover; }
.p3-cmp-tag { position: absolute; top: 0; overflow: hidden; pointer-events: none; text-align: left; }
.p3-cmp-tag span { display: inline-block; max-width: calc(100% - 16px); margin: 8px; padding: 2px 8px; font: 600 0.75rem/1.6 sans-serif; color: #fff; background: rgba(0, 0, 0, 0.6); border-radius: 3px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; vertical-align: top; }
.p3-cmp-handle { position: absolute; top: 0; bottom: 0; width: 44px; margin-left: -22px; cursor: ew-resize; touch-action: pan-y; outline: none; }
.p3-cmp-handle::before { content: ""; position: absolute; top: 0; bottom: 0; left: 50%; width: 2px; margin-left: -1px; background: #e8973a; }
.p3-cmp-knob { position: absolute; top: 50%; left: 50%; width: 34px; height: 34px; margin: -17px 0 0 -17px; border-radius: 50%; background: #e8973a; color: #111; font: 700 16px/34px sans-serif; letter-spacing: -1px; text-align: center; box-shadow: 0 1px 6px rgba(0, 0, 0, 0.45); }
.p3-cmp-handle:focus-visible .p3-cmp-knob { box-shadow: 0 0 0 3px rgba(232, 151, 58, 0.55); }
.p3-cmp figcaption { margin-top: 0.5em; font-size: 0.9em; font-style: italic; line-height: 1.4; color: inherit; opacity: 0.85; text-align: center; }
@media (max-width: 560px) { .p3-grid.c3, .p3-grid.c4 { --n: 2; } }
</style>

<script>
(function () {
  function lum(c) { var m = c.match(/[\d.]+/g); if (!m || (m.length > 3 && parseFloat(m[3]) === 0)) { return null; } return (0.299 * m[0] + 0.587 * m[1] + 0.114 * m[2]) / 255; }
  function pageIsDark() {
    var els = [document.querySelector('.page__content'), document.querySelector('article'), document.body, document.documentElement];
    for (var i = 0; i < els.length; i++) { if (els[i]) { var l = lum(getComputedStyle(els[i]).backgroundColor); if (l !== null) { return l < 0.5; } } }
    return window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
  }
  function apply() { document.documentElement.setAttribute('data-p3-mode', pageIsDark() ? 'dark' : 'light'); }
  apply();
  window.addEventListener('DOMContentLoaded', apply);
  window.addEventListener('load', apply);
  var mo = new MutationObserver(function () { setTimeout(apply, 50); });
  mo.observe(document.documentElement, { attributes: true, attributeFilter: ['class', 'data-theme', 'style', 'data-mode', 'data-bs-theme'] });
  document.addEventListener('DOMContentLoaded', function () { mo.observe(document.body, { attributes: true, attributeFilter: ['class', 'data-theme', 'style'] }); });
  if (window.matchMedia) { window.matchMedia('(prefers-color-scheme: dark)').addEventListener('change', apply); }
})();
</script>

<script>
(function () {
  function init(fig) {
    var imgs = Array.prototype.filter.call(fig.children, function (c) { return c.tagName === 'IMG'; });
    var n = imgs.length;
    if (n < 2) { return; }
    var labels = (fig.getAttribute('data-labels') || '').split('|');
    var stage = document.createElement('div');
    stage.className = 'p3-cmp-stage';
    fig.insertBefore(stage, imgs[0]);
    var pos = [], tags = [], handles = [], k;
    for (k = 0; k < n; k++) {
      stage.appendChild(imgs[k]);
      if (k > 0) { imgs[k].className += ' p3-layer'; pos.push(100 * k / n); }
    }
    for (k = 0; k < n; k++) {
      var box = document.createElement('div'), chip = document.createElement('span');
      box.className = 'p3-cmp-tag';
      chip.textContent = labels[k] || '';
      box.appendChild(chip); stage.appendChild(box); tags.push(box);
    }
    for (k = 0; k < n - 1; k++) {
      var h = document.createElement('div');
      h.className = 'p3-cmp-handle';
      h.setAttribute('role', 'slider'); h.setAttribute('tabindex', '0');
      h.setAttribute('aria-label', 'Comparison slider ' + (k + 1));
      h.setAttribute('aria-valuemin', '0'); h.setAttribute('aria-valuemax', '100');
      h.innerHTML = '<span class="p3-cmp-knob">&lsaquo;&rsaquo;</span>';
      stage.appendChild(h); handles.push(h);
    }
    function render() {
      for (var i = 0; i < n; i++) {
        var s = i === 0 ? 0 : pos[i - 1], e = i === n - 1 ? 100 : pos[i];
        if (i > 0) {
          var clip = 'inset(0 ' + (100 - e) + '% 0 ' + s + '%)';
          imgs[i].style.clipPath = clip; imgs[i].style.webkitClipPath = clip;
        }
        tags[i].style.left = s + '%'; tags[i].style.width = (e - s) + '%';
      }
      for (var j = 0; j < handles.length; j++) {
        handles[j].style.left = pos[j] + '%';
        handles[j].setAttribute('aria-valuenow', String(Math.round(pos[j])));
      }
    }
    function setPos(i, v) {
      var lo = (i > 0 ? pos[i - 1] : 0) + 4, hi = (i < n - 2 ? pos[i + 1] : 100) - 4;
      pos[i] = Math.max(lo, Math.min(hi, v)); render();
    }
    function xPct(ev) { var r = stage.getBoundingClientRect(); return (ev.clientX - r.left) / r.width * 100; }
    function nearest(x) {
      var best = 0, d = Infinity;
      for (var i = 0; i < pos.length; i++) { var dd = Math.abs(pos[i] - x); if (dd < d) { d = dd; best = i; } }
      return best;
    }
    var active = null;
    stage.addEventListener('pointerdown', function (ev) {
      if (ev.button) { return; }
      var x = xPct(ev); active = nearest(x); setPos(active, x);
      try { stage.setPointerCapture(ev.pointerId); } catch (err) {}
      ev.preventDefault();
    });
    stage.addEventListener('pointermove', function (ev) { if (active !== null) { setPos(active, xPct(ev)); } });
    ['pointerup', 'pointercancel', 'lostpointercapture'].forEach(function (name) {
      stage.addEventListener(name, function () { active = null; });
    });
    handles.forEach(function (hd, i) {
      hd.addEventListener('keydown', function (ev) {
        if (ev.key === 'ArrowLeft') { setPos(i, pos[i] - 2); ev.preventDefault(); }
        if (ev.key === 'ArrowRight') { setPos(i, pos[i] + 2); ev.preventDefault(); }
      });
    });
    render();
    fig.className += ' ready';
  }
  function run() { Array.prototype.forEach.call(document.querySelectorAll('.p3-cmp'), init); }
  if (document.readyState === 'loading') { document.addEventListener('DOMContentLoaded', run); } else { run(); }
})();
</script>

## Overview

In this project I recover homographies from hand-clicked correspondences, warp images with inverse warping (nearest neighbor and bilinear, both written from scratch), rectify planar objects, and blend overlapping photos into mosaics with distance-transform feathering. Everything is done in homogeneous coordinates with only NumPy for linear algebra; no `cv2.findHomography` or `cv2.warpPerspective`.

Convention used throughout: the origin $(0,0)$ is the **top-left** pixel, $x$ is the column (increasing to the right), $y$ is the row (increasing downward), and images are read from top-left to bottom-right. In NumPy an image is indexed as `im[y, x]`.

## A.1: Shoot the Pictures

A homography only relates two photos exactly when the camera **rotates about its center of projection** (or the scene is planar). I shot several pairs with substantial overlap and as close together in time as I could, so lighting and moving subjects change as little as possible.

**Set 1: second floor of Hearst Memorial Mining Building (HMMB).** Left and right views of the same balcony.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj4/output/A1/hmmb_rotation.png" alt="Second floor of HMMB taken from the left and right side" loading="lazy">
    <figcaption>Set 1: second floor of HMMB, taken from the left and from the right</figcaption>
  </figure>
</div>

**Set 2: Wheeler Hall.** A second left/right pair with a fixed viewpoint. All of my other pairs (Martinez, Moffitt, Blackwell hallway and stairs, Campanile, Upper Sproul, Sather Gate) are shown with their mosaics in A.4.

<div class="p3-grid c2 medium">
  <figure>
    <img src="/images/cs180/proj4/images/A4/wheeler_left.jpg" alt="Wheeler Hall, left photo" loading="lazy">
    <figcaption>Set 2: Wheeler Hall, left</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/wheeler_right.jpg" alt="Wheeler Hall, right photo" loading="lazy">
    <figcaption>Set 2: Wheeler Hall, right</figcaption>
  </figure>
</div>

<div class="p3-note" markdown="1">

**A mistake I only understood later.** For the HMMB pair the correspondences look good (A.2), but when I got to A.4 I realized I had misread the instructions: I rotated my *hand* (about the axis of my body) instead of rotating the *phone* about its own center. That adds a large translation between the two viewpoints, which a homography cannot model, and it is why my first batch of mosaics stitched badly. The sets in A.4 are the ones that stitched acceptably.

</div>

## A.2: Recover Homographies

A homography maps a point $p=(x,y,1)^\top$ in image 1 to $p'\sim(x',y',1)^\top$ in image 2:

$$
p' \sim H p,\qquad H=\begin{bmatrix}h_1&h_2&h_3\\h_4&h_5&h_6\\h_7&h_8&1\end{bmatrix}.
$$

$H$ is only defined up to scale, so fixing $h_9=1$ leaves **8 unknowns**. Dividing out the third coordinate and multiplying through gives two *linear* equations per correspondence:

$$
\underbrace{\begin{bmatrix}x&y&1&0&0&0&-x'x&-x'y\\0&0&0&x&y&1&-y'x&-y'y\end{bmatrix}}_{2\times 8}
\underbrace{\begin{bmatrix}h_1\\\vdots\\h_8\end{bmatrix}}_{8\times1}
=\underbrace{\begin{bmatrix}x'\\y'\end{bmatrix}}_{2\times1}.
$$

Stacking $n$ correspondences gives $A\,h=b$ with $A\in\mathbb{R}^{2n\times 8}$ and $b\in\mathbb{R}^{2n}$. Four correspondences give a square system, but it is very sensitive to click error. I used **8 to 13** correspondences per pair (12 for HMMB, so $A$ is $24\times8$) and solve the overdetermined system by least squares, $\hat h=\arg\min_h\|Ah-b\|_2^2$. More correspondences average out click noise, so more is better.

#### Why I normalize the points first

Without normalization, least squares did not work well. Pixel coordinates are in the thousands, so the columns holding $x'x$ and $x'y$ are around $10^6$–$10^7$ while the column of ones is $1$. Points with large $x,y$ dominate the residual, and the solution is wrecked by the huge difference in column scales. My points were also tightly clustered in one corner of the image, which makes things worse.

The fix is to translate each point set so its centroid is at the origin and scale it so the mean distance to the origin is $\sqrt2$, using similarity transforms $T_1$, $T_2$. I solve for $\tilde H$ on the normalized points and undo it:

$$
H = T_2^{-1}\,\tilde H\,T_1 .
$$

For the HMMB pair this changes the system dramatically:

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th></th><th>$\kappa(A)$</th><th>mean reprojection error</th><th>max error</th></tr></thead>
  <tbody>
    <tr><td>No normalization</td><td>$5.3\times10^{9}$</td><td>11.5 px</td><td>22.4 px</td></tr>
    <tr><td>With normalization</td><td>$4.3$</td><td>5.1 px</td><td>10.3 px</td></tr>
  </tbody>
</table>

The condition number $\kappa(A)=\sigma_{\max}/\sigma_{\min}$ (from EECS 127) tells how much input noise the least-squares solution amplifies. At $10^{9}$ the unnormalized system is nearly singular; at $4.3$ it is well conditioned.

#### Correspondences and recovered homography (HMMB)

The figure shows my 12 clicked correspondences on image 1 and image 2 (circles), and on the third panel the points of image 1 pushed through $H$ (crosses) with a line to the clicked target in image 2.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj4/output/A2/a2_correspondences.png" alt="Correspondences on image 1, image 2, and the reprojection of image 1 points through H" loading="lazy">
    <figcaption>Left: points on image 1. Middle: matching points on image 2. Right: image 1 points reprojected through $H$ onto image 2</figcaption>
  </figure>
</div>

$$
H=\begin{bmatrix}2.73001&-0.04943&-5947.33837\\0.83506&2.65121&-2699.60734\\0.00047&0.00007&1\end{bmatrix}
$$

The reprojection errors per point range from $1.7$ to $10.3$ pixels (mean $5.1$) on photos that are about 3000 pixels tall. That is small for the image size, but larger than my A.4 sets (about $0.3$–$0.4$ px at quarter resolution). That is the translation problem from A.1 showing up: the HMMB pair is not a pure rotation, so no single homography fits all points exactly.

<div class="p3-note" markdown="1">

**Using the determinant and condition number as sanity checks.** $\kappa(A)$ tells me whether the *system* is trustworthy (above). The determinant of the upper-left $2\times2$ block of $H$ is a rough area-scale indicator: near $0$ means the warp collapses the image, negative means a reflection, and a very large value means a huge zoom. It is not an exact local scale, since the bottom row of $H$ also matters. For HMMB it is $7.3$, a warning sign that matches the large translation. For my good mosaics it is between $1.1$ and $1.8$ (one exception is discussed in A.4).

</div>

## A.3: Warp the Images

#### Inverse warping

Forward warping (push every source pixel to $Hx$) leaves holes. Instead, I loop over every pixel $b$ of the **output** canvas, ask where it came from in the source, and sample there:

<svg viewBox="0 0 640 170" role="img" aria-label="Inverse warping: for each output pixel b, compute x = H inverse b in the source image, then sample the source" style="display:block; max-width:640px; width:100%; height:auto; margin:1.2em auto;" fill="none" stroke="currentColor" stroke-width="1.6" font-family="sans-serif" font-size="14">
  <rect x="20" y="30" width="170" height="110" rx="6"/>
  <text x="105" y="60" text-anchor="middle" fill="currentColor" stroke="none">source image</text>
  <circle cx="105" cy="100" r="5" fill="currentColor"/>
  <text x="105" y="125" text-anchor="middle" fill="currentColor" stroke="none">x (sample here)</text>
  <polygon points="450,40 610,25 625,140 440,125" />
  <text x="530" y="60" text-anchor="middle" fill="currentColor" stroke="none">output canvas</text>
  <circle cx="530" cy="95" r="5" fill="currentColor"/>
  <text x="530" y="118" text-anchor="middle" fill="currentColor" stroke="none">b (fill this pixel)</text>
  <path d="M112 90 C 220 40, 380 40, 522 90" stroke-dasharray="5 4"/>
  <text x="318" y="38" text-anchor="middle" fill="currentColor" stroke="none">b = H x (defines the canvas)</text>
  <path d="M522 105 C 380 160, 220 160, 112 108"/>
  <polygon points="112,108 124,100 126,113" fill="currentColor" stroke="none"/>
  <text x="318" y="160" text-anchor="middle" fill="currentColor" stroke="none">x = H⁻¹ b, then divide by the third coordinate</text>
</svg>

The path is **x → b → x**: the forward map $H$ is only used to find the output bounding box, then each output pixel $b$ is mapped back with $H^{-1}$, and its color is read from the source at the (generally non-integer) location $x$:

$$
x \sim H^{-1} b,\qquad (x,y)=\left(\tfrac{x_1}{x_3},\tfrac{y_1}{x_3}\right)\ \text{where}\ (x_1,y_1,x_3)^\top=H^{-1}b .
$$

**Output size.** I push the four source corners through $H$, divide by the third coordinate, and take the min and max of the results to get the output bounding box. For mosaics and rectification the canvas can be offset (negative coordinates), so every output pixel is shifted by the box's top-left corner before applying $H^{-1}$.

**Missing pixels.** Output pixels whose preimage falls outside the source get an alpha of $0$ (black, and later ignored by the blend); all others get alpha $1$.

#### Two interpolation methods (both from scratch)

**Nearest neighbor** rounds the source location to the closest pixel: $I_{\text{out}}(b)=I\big(\operatorname{round}(x),\operatorname{round}(y)\big)$.

**Bilinear** takes a weighted average of the four surrounding pixels. With $x_0=\lfloor x\rfloor$, $y_0=\lfloor y\rfloor$, $a=x-x_0$, $b_y=y-y_0$:

$$
I(x,y)=(1-a)(1-b_y)\,I_{x_0,y_0}+a(1-b_y)\,I_{x_0+1,y_0}+(1-a)\,b_y\,I_{x_0,y_0+1}+a\,b_y\,I_{x_0+1,y_0+1}.
$$

Both are vectorized over the whole canvas at once rather than looping over pixels.

#### Rectification

To test the warp I rectify rectangular objects in single photos. I click the four corners of the object, order them top-left, top-right, bottom-right, bottom-left, and map them to an axis-aligned rectangle whose width and height are the average lengths of the opposite edges. The output canvas is cropped to exactly this rectangle, so only the object is shown. Four correspondences determine $H$ exactly here, which is fine because the target rectangle is exact (the instability warning only applies to noisy clicks).

**Rectification 1: Berkeley campus historical landmark** (photo rotated by $90^\circ$ before clicking).

<div class="p3-grid c3">
  <figure>
  <div style="position:relative; aspect-ratio:3/4; overflow:hidden;">
    <img src="/images/cs180/proj4/images/A3/campus.jpg" alt="Original campus landmark photo" loading="lazy" style="position:absolute; top:50%; left:50%; width:133.333%; max-width:none; height:auto; transform:translate(-50%,-50%) rotate(-90deg);">
  </div>
  <figcaption>Original</figcaption>
</figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/campus_nearest.png" alt="Rectified campus landmark, nearest neighbor" loading="lazy">
    <figcaption>Nearest neighbor</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/campus_bilinear.png" alt="Rectified campus landmark, bilinear" loading="lazy">
    <figcaption>Bilinear</figcaption>
  </figure>
</div>

<div class="p3-grid c2 medium">
  <figure>
    <img src="/images/cs180/proj4/output/A3/campus_nearest_zoom.png" alt="Zoomed crop, nearest neighbor" loading="lazy">
    <figcaption>Zoom: nearest neighbor</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/campus_bilinear_zoom.png" alt="Zoomed crop, bilinear" loading="lazy">
    <figcaption>Zoom: bilinear</figcaption>
  </figure>
</div>

**Rectification 2: US Embassy in South Korea.**

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A3/embassy.jpg" alt="Original embassy photo" loading="lazy">
    <figcaption>Original</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/embassy_nearest.png" alt="Rectified embassy, nearest neighbor" loading="lazy">
    <figcaption>Nearest neighbor</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/embassy_bilinear.png" alt="Rectified embassy, bilinear" loading="lazy">
    <figcaption>Bilinear</figcaption>
  </figure>
</div>

<div class="p3-grid c2 medium">
  <figure>
    <img src="/images/cs180/proj4/output/A3/embassy_nearest_zoom.png" alt="Zoomed crop, nearest neighbor" loading="lazy">
    <figcaption>Zoom: nearest neighbor</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/embassy_bilinear_zoom.png" alt="Zoomed crop, bilinear" loading="lazy">
    <figcaption>Zoom: bilinear</figcaption>
  </figure>
</div>

**Rectification 3: California Nursery Company poster.**

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A3/nursery.jpg" alt="Original nursery poster photo" loading="lazy">
    <figcaption>Original</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/nursery_nearest.png" alt="Rectified nursery poster, nearest neighbor" loading="lazy">
    <figcaption>Nearest neighbor</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/nursery_bilinear.png" alt="Rectified nursery poster, bilinear" loading="lazy">
    <figcaption>Bilinear</figcaption>
  </figure>
</div>

<div class="p3-grid c2 medium">
  <figure>
    <img src="/images/cs180/proj4/output/A3/nursery_nearest_zoom.png" alt="Zoomed crop, nearest neighbor" loading="lazy">
    <figcaption>Zoom: nearest neighbor</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A3/nursery_blinear_zoom.png" alt="Zoomed crop, bilinear" loading="lazy">
    <figcaption>Zoom: bilinear</figcaption>
  </figure>
</div>

#### Nearest neighbor vs. bilinear

Nearest neighbor is the cheapest option: one rounding and one pixel lookup per output pixel. But every output pixel copies a whole source pixel, so slanted edges and thin text become jagged "staircases" and blocky detail appears wherever the warp stretches the image. Bilinear costs four lookups and a weighted sum per channel, but it blends neighbors smoothly, so edges and text look clean at the price of slightly softer detail. Both are fully vectorized, and the extra cost of bilinear was small compared with the quality gain, so I use bilinear for everything in A.4.

## A.4: Blend the Images into a Mosaic

#### Procedure

1. **Correspondences and $H$.** Click 8 to 13 matching points in the overlap, then compute the normalized least-squares $H$ that maps the left image into the right image's frame. The right image stays unwarped (it is the reference).
2. **Canvas size.** Push the left image's four corners through $H$ and combine them with the right image's corners. The bounding box of all eight is the canvas.
3. **Warp both images** into this canvas with bilinear inverse warping (the reference image is just shifted by the canvas offset). Each gets its alpha mask.
4. **Feather with a distance transform.** For each image, `cv2.distanceTransform` assigns every covered pixel its distance to the nearest empty pixel in that image's mask. This is $0$ at the image border and grows toward the interior, so it acts as a linear-falloff alpha. I pad the mask with a one-pixel zero border so edges that touch the canvas boundary also fall to $0$.
5. **Weighted average.** With warped images $r_1,r_2$ and distances $d_1,d_2$:

$$
r=\frac{d_1\,r_1+d_2\,r_2}{d_1+d_2}.
$$

In the overlap both images contribute, and each fades out smoothly toward its own edge, so the seam disappears instead of showing a hard cut. Where only one image exists the formula returns that image, and where neither exists the denominator is clamped to a tiny value so the pixel stays black.

**Practical issue: memory.** My original photos were so large that the full-resolution canvases made memory blow up. I shrank every image to a quarter of its resolution before computing and blending the mosaics.

#### Why these photos are boring

My first batch of A.4 photos was more interesting, but the stitching was bad: I rotated my arm instead of rotating the camera about its own axis, so the morning photos had far too much translation and not just rotation (see the note in A.1). So you now get to enjoy plain photos of Blackwell Residence Hall and pictures I re-shot on campus the next morning.

#### Mosaic 1: Wheeler Hall

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A4/wheeler_left.jpg" alt="Wheeler left source" loading="lazy">
    <figcaption>Source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/wheeler_right.jpg" alt="Wheeler right source" loading="lazy">
    <figcaption>Source (right, reference)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/wheeler.jpg" alt="Wheeler mosaic" loading="lazy">
    <figcaption>Mosaic</figcaption>
  </figure>
</div>

#### Mosaic 2: Moffitt Library

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A4/moffit_left.jpg" alt="Moffitt left source" loading="lazy">
    <figcaption>Source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/moffit_right.jpg" alt="Moffitt right source" loading="lazy">
    <figcaption>Source (right, reference)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/moffitt.jpg" alt="Moffitt mosaic" loading="lazy">
    <figcaption>Mosaic</figcaption>
  </figure>
</div>

#### Mosaic 3: Martinez

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A4/martinez_left.jpg" alt="Martinez left source" loading="lazy">
    <figcaption>Source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/martinez_right.jpg" alt="Martinez right source" loading="lazy">
    <figcaption>Source (right, reference)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/martinez.jpg" alt="Martinez mosaic" loading="lazy">
    <figcaption>Mosaic</figcaption>
  </figure>
</div>

#### Extra mosaics: Blackwell Residence Hall

The hallway pair is a vertical mosaic (up and down views), and the stairs are left/right.

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A4/hallway_up.jpg" alt="Hallway up source" loading="lazy">
    <figcaption>Hallway, source (up)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/hallway_down.jpg" alt="Hallway down source" loading="lazy">
    <figcaption>Hallway, source (down)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/blackwell/hallway.jpg" alt="Hallway mosaic" loading="lazy">
    <figcaption>Hallway mosaic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/stairs_left.jpg" alt="Stairs left source" loading="lazy">
    <figcaption>Stairs, source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/stairs_right.jpg" alt="Stairs right source" loading="lazy">
    <figcaption>Stairs, source (right)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/blackwell/stairs.jpg" alt="Stairs mosaic" loading="lazy">
    <figcaption>Stairs mosaic</figcaption>
  </figure>
</div>

#### Extra mosaics: campus photos from the next morning

<div class="p3-grid c3">
  <figure>
    <img src="/images/cs180/proj4/images/A4/tower_left.jpg" alt="Campanile left source" loading="lazy">
    <figcaption>Campanile, source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/tower_right.jpg" alt="Campanile right source" loading="lazy">
    <figcaption>Campanile, source (right)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/campus_photos/tower.jpg" alt="Campanile mosaic" loading="lazy">
    <figcaption>Campanile mosaic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/sproul_left.jpg" alt="Upper Sproul left source" loading="lazy">
    <figcaption>Upper Sproul, source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/sproul_right.jpg" alt="Upper Sproul right source" loading="lazy">
    <figcaption>Upper Sproul, source (right)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/campus_photos/upper_sproul.jpg" alt="Upper Sproul mosaic" loading="lazy">
    <figcaption>Upper Sproul mosaic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/gate_left.jpg" alt="Sather Gate left source" loading="lazy">
    <figcaption>Sather Gate, source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/A4/gate_right.jpg" alt="Sather Gate right source" loading="lazy">
    <figcaption>Sather Gate, source (right)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/campus_photos/sather_gate.jpg" alt="Sather Gate mosaic" loading="lazy">
    <figcaption>Sather Gate mosaic</figcaption>
  </figure>
</div>

#### Fit quality of the mosaic homographies

Errors are in pixels at the quarter-resolution used for blending.

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Set</th><th>mean reprojection error</th><th>$\det H_{[1:2,1:2]}$</th></tr></thead>
  <tbody>
    <tr><td>Wheeler</td><td>0.34 px</td><td>1.46</td></tr>
    <tr><td>Moffitt</td><td>0.43 px</td><td>8.14</td></tr>
    <tr><td>Campanile</td><td>0.32 px</td><td>1.65</td></tr>
    <tr><td>Upper Sproul</td><td>0.42 px</td><td>1.83</td></tr>
    <tr><td>Sather Gate</td><td>0.34 px</td><td>1.09</td></tr>
  </tbody>
</table>

All of these are well under a pixel, which is why the seams are hard to see. Moffitt stands out with a determinant of $8.1$: its two views differ a lot in perspective, so the warp is strong and the left image is visibly stretched in the mosaic. The error is still low because the points fit the homography well; the large determinant just tells me the warp is strong.

#### What still goes wrong

- **Ghosting and wedges** appear where the photos are not related by a pure rotation (any remaining translation) or where people and leaves moved between shots. Distance-transform feathering cannot hide misalignment; a Laplacian pyramid would reduce ghosting of high-frequency detail.
- **Exposure differences** between the two photos show up as a soft brightness gradient across the overlap.
- **Click error** of a couple of pixels changes the homography noticeably, which is why I used more than four correspondences, spread across the overlap, and normalized them.

## Conclusion

The most important thing I learned is that the math is only half of the project; the data matters just as much. A homography assumes a pure rotation about the camera center, and when I rotated my arm instead of the camera, no amount of careful least squares could fix the mosaics. On the math side, normalizing the points reduced $\kappa(A)$ from about $10^{9}$ to about $4$ and halved the reprojection error, and the determinant of $H$ and $\kappa(A)$ from EECS 127 turned out to be quick ways to judge whether a recovered system is trustworthy. Inverse warping avoids holes, bilinear interpolation gives much cleaner results than nearest neighbor for a small cost, and distance-transform feathering gives a simple, effective way to blend.