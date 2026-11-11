---
title: "Project 4: Image Warping and Mosaicing"
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

**Part A.** I recover homographies from hand-clicked correspondences, warp images with inverse warping (nearest neighbor and bilinear, both written from scratch), rectify planar objects, and blend overlapping photos into mosaics with distance-transform feathering.

**Part B.** I remove the clicking. Harris corners are thinned with adaptive non-maximal suppression (ANMS), described by normalized $8\times8$ patches, matched with a nearest-neighbor ratio test, and filtered with 4-point RANSAC to get a robust homography. The result is a fully automatic stitcher, following the MOPS paper by Brown, Szeliski and Winder ([Multi-Image Matching using Multi-Scale Oriented Patches](https://cal-cs180.github.io/fa26/hw/proj3/Papers/MOPS.pdf), CVPR 2005).

Everything is done in homogeneous coordinates with only NumPy for linear algebra; no `cv2.findHomography`, `cv2.warpPerspective`, or OpenCV feature matchers.

Convention: the origin $(0,0)$ is the **top-left** pixel, $x$ is the column (increasing to the right), $y$ is the row (increasing downward), and images are read from top-left to bottom-right. In NumPy an image is indexed as `im[y, x]`. Warping and homographies use $(x, y)$. In Part B the corner detector returns points as $(y, x)$ (row, column), so I store them that way and flip to $(x, y)$ only right before computing $H$. Each section says which order it uses.

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

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj4/output/A1/wheeler_rotation.png" alt="Wheeler Hall taken from the left and right side" loading="lazy">
    <figcaption>Set 2: Wheeler Hall, taken from the left and from the right</figcaption>
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

#### The system of equations (HMMB)

For one correspondence, e.g. correspondence 1, $(x,y)=(2348,2614)\mapsto(x',y')=(149,2707)$, the two rows are

$$
\begin{bmatrix}2348&2614&1&0&0&0&-349852&-389486\\0&0&0&2348&2614&1&-6356036&-7076098\end{bmatrix}
\begin{bmatrix}h_1\\\vdots\\h_8\end{bmatrix}
=\begin{bmatrix}149\\2707\end{bmatrix}.
$$

Stacking all 12 correspondences gives the full $24\times8$ system $A\,h=b$, shown below with the raw pixel coordinates (before normalization). The entries span from $1$ to about $8.5\times10^{6}$, which is exactly the column-scale problem that gives $\kappa(A)\approx5.3\times10^{9}$. In practice I build and solve the same system on the normalized points.

<details markdown="0">
<summary>Show the full $24\times8$ system $A h=b$</summary>
<div style="overflow-x:auto; font-size:0.8em;">
<table class="p3-table" style="margin:1em auto;">
<thead><tr><th>$h_1$</th><th>$h_2$</th><th>$h_3$</th><th>$h_4$</th><th>$h_5$</th><th>$h_6$</th><th>$h_7$</th><th>$h_8$</th><th>$b$</th></tr></thead>
<tbody>
<tr><td>2&#8201;348</td><td>2&#8201;614</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-349&#8201;852</td><td>-389&#8201;486</td><td>149</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;348</td><td>2&#8201;614</td><td>1</td><td>-6&#8201;356&#8201;036</td><td>-7&#8201;076&#8201;098</td><td>2&#8201;707</td></tr>
<tr><td>2&#8201;341</td><td>2&#8201;365</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-341&#8201;786</td><td>-345&#8201;290</td><td>146</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;341</td><td>2&#8201;365</td><td>1</td><td>-5&#8201;697&#8201;994</td><td>-5&#8201;756&#8201;410</td><td>2&#8201;434</td></tr>
<tr><td>2&#8201;453</td><td>2&#8201;614</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-667&#8201;216</td><td>-711&#8201;008</td><td>272</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;453</td><td>2&#8201;614</td><td>1</td><td>-6&#8201;601&#8201;023</td><td>-7&#8201;034&#8201;274</td><td>2&#8201;691</td></tr>
<tr><td>2&#8201;450</td><td>2&#8201;337</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-649&#8201;250</td><td>-619&#8201;305</td><td>265</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;450</td><td>2&#8201;337</td><td>1</td><td>-5&#8201;865&#8201;300</td><td>-5&#8201;594&#8201;778</td><td>2&#8201;394</td></tr>
<tr><td>2&#8201;387</td><td>2&#8201;819</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-432&#8201;047</td><td>-510&#8201;239</td><td>181</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;387</td><td>2&#8201;819</td><td>1</td><td>-6&#8201;960&#8201;492</td><td>-8&#8201;220&#8201;204</td><td>2&#8201;916</td></tr>
<tr><td>2&#8201;387</td><td>2&#8201;871</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-432&#8201;047</td><td>-519&#8201;651</td><td>181</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;387</td><td>2&#8201;871</td><td>1</td><td>-7&#8201;084&#8201;616</td><td>-8&#8201;521&#8201;128</td><td>2&#8201;968</td></tr>
<tr><td>2&#8201;555</td><td>2&#8201;831</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-947&#8201;905</td><td>-1&#8201;050&#8201;301</td><td>371</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;555</td><td>2&#8201;831</td><td>1</td><td>-7&#8201;389&#8201;060</td><td>-8&#8201;187&#8201;252</td><td>2&#8201;892</td></tr>
<tr><td>2&#8201;569</td><td>2&#8201;884</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-978&#8201;789</td><td>-1&#8201;098&#8201;804</td><td>381</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;569</td><td>2&#8201;884</td><td>1</td><td>-7&#8201;552&#8201;860</td><td>-8&#8201;478&#8201;960</td><td>2&#8201;940</td></tr>
<tr><td>2&#8201;619</td><td>2&#8201;643</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-1&#8201;165&#8201;455</td><td>-1&#8201;176&#8201;135</td><td>445</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;619</td><td>2&#8201;643</td><td>1</td><td>-7&#8201;058&#8201;205</td><td>-7&#8201;122&#8201;885</td><td>2&#8201;695</td></tr>
<tr><td>2&#8201;650</td><td>2&#8201;659</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-1&#8201;279&#8201;950</td><td>-1&#8201;284&#8201;297</td><td>483</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;650</td><td>2&#8201;659</td><td>1</td><td>-7&#8201;131&#8201;150</td><td>-7&#8201;155&#8201;369</td><td>2&#8201;691</td></tr>
<tr><td>2&#8201;612</td><td>2&#8201;554</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-1&#8201;144&#8201;056</td><td>-1&#8201;118&#8201;652</td><td>438</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;612</td><td>2&#8201;554</td><td>1</td><td>-6&#8201;775&#8201;528</td><td>-6&#8201;625&#8201;076</td><td>2&#8201;594</td></tr>
<tr><td>2&#8201;661</td><td>2&#8201;554</td><td>1</td><td>0</td><td>0</td><td>0</td><td>-1&#8201;285&#8201;263</td><td>-1&#8201;233&#8201;582</td><td>483</td></tr>
<tr><td>0</td><td>0</td><td>0</td><td>2&#8201;661</td><td>2&#8201;554</td><td>1</td><td>-6&#8201;891&#8201;990</td><td>-6&#8201;614&#8201;860</td><td>2&#8201;590</td></tr>
</tbody>
</table>
</div>
</details>

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

**Vectorizing the loop.** My first version used two nested `for` loops over the rows and columns of the canvas, which was slow at these image sizes. I replaced them with `np.meshgrid`, which builds the $(x, y)$ coordinates of every output pixel at once. Stacking them with a row of ones gives a single $3\times(H_{\text{out}}W_{\text{out}})$ matrix $B$, so the whole inverse map is one matrix product $H^{-1}B$ followed by one division by the third row.

**Output size.** I push the four source corners through $H$, divide by the third coordinate, and take the min and max of the results to get the output bounding box. For mosaics and rectification the canvas can be offset (negative coordinates), so every output pixel is shifted by the box's top-left corner before applying $H^{-1}$.

**Missing pixels.** Output pixels whose preimage falls outside the source get an alpha of $0$ (black, and later ignored by the blend); all others get alpha $1$.

#### Two interpolation methods (both from scratch)

**Nearest neighbor** rounds the source location to the closest pixel: $I_{\text{out}}(b)=I\big(\operatorname{round}(x),\operatorname{round}(y)\big)$.

**Bilinear** takes a weighted average of the four surrounding pixels. With $x_0=\lfloor x\rfloor$, $y_0=\lfloor y\rfloor$, $a=x-x_0$, $b_y=y-y_0$:

$$
I(x,y)=(1-a)(1-b_y)\,I_{x_0,y_0}+a(1-b_y)\,I_{x_0+1,y_0}+(1-a)\,b_y\,I_{x_0,y_0+1}+a\,b_y\,I_{x_0+1,y_0+1}.
$$

Both use the meshgrid coordinates above, so they run over the whole canvas at once rather than looping over pixels.

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
    <img src="/images/cs180/proj4/output/A3/nursery_bilinear_zoom.png" alt="Zoomed crop, bilinear" loading="lazy">
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

## Part B: Feature Matching for Autostitching

Clicking 8 to 13 points per pair was the slowest and most error-prone step of Part A. Part B replaces it with a pipeline from the MOPS paper ([Brown et al., 2005](https://cal-cs180.github.io/fa26/hw/proj3/Papers/MOPS.pdf)), and everything after $H$ (warping and blending) is reused from Part A.

<svg viewBox="0 0 640 120" role="img" aria-label="Autostitching pipeline: Harris corners, ANMS, 8 by 8 descriptors, ratio-test matching, RANSAC, warp and blend" style="display:block; max-width:640px; width:100%; height:auto; margin:1.2em auto;" fill="none" stroke="currentColor" stroke-width="1.6" font-family="sans-serif" font-size="12">
  <defs><marker id="p4arr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto"><path d="M0,0 L10,5 L0,10 z" fill="currentColor" stroke="none"/></marker></defs>
  <rect x="2" y="30" width="88" height="56" rx="6"/>
  <text x="46" y="54" text-anchor="middle" fill="currentColor" stroke="none">Harris</text>
  <text x="46" y="70" text-anchor="middle" fill="currentColor" stroke="none">corners</text>
  <rect x="110" y="30" width="88" height="56" rx="6"/>
  <text x="154" y="54" text-anchor="middle" fill="currentColor" stroke="none">strong +</text>
  <text x="154" y="70" text-anchor="middle" fill="currentColor" stroke="none">ANMS</text>
  <rect x="218" y="30" width="88" height="56" rx="6"/>
  <text x="262" y="54" text-anchor="middle" fill="currentColor" stroke="none">8×8</text>
  <text x="262" y="70" text-anchor="middle" fill="currentColor" stroke="none">descriptors</text>
  <rect x="326" y="30" width="88" height="56" rx="6"/>
  <text x="370" y="54" text-anchor="middle" fill="currentColor" stroke="none">ratio-test</text>
  <text x="370" y="70" text-anchor="middle" fill="currentColor" stroke="none">matching</text>
  <rect x="434" y="30" width="88" height="56" rx="6"/>
  <text x="478" y="54" text-anchor="middle" fill="currentColor" stroke="none">RANSAC</text>
  <text x="478" y="70" text-anchor="middle" fill="currentColor" stroke="none">→ H</text>
  <rect x="542" y="30" width="96" height="56" rx="6" stroke-dasharray="5 4"/>
  <text x="590" y="54" text-anchor="middle" fill="currentColor" stroke="none">warp + blend</text>
  <text x="590" y="70" text-anchor="middle" fill="currentColor" stroke="none">(Part A)</text>
  <path d="M90 58 H108" marker-end="url(#p4arr)"/>
  <path d="M198 58 H216" marker-end="url(#p4arr)"/>
  <path d="M306 58 H324" marker-end="url(#p4arr)"/>
  <path d="M414 58 H432" marker-end="url(#p4arr)"/>
  <path d="M522 58 H540" marker-end="url(#p4arr)"/>
  <text x="208" y="108" text-anchor="middle" fill="currentColor" stroke="none">B.1 – B.2: run on each image separately</text>
  <text x="478" y="108" text-anchor="middle" fill="currentColor" stroke="none">B.3 – B.4: run on the pair</text>
</svg>

## B.1: Harris Corner Detection

#### Workflow

1. **Grayscale** the image and compute the Harris response $h$ at every pixel ($\sigma=1$).
2. **Local maxima** of $h$ become candidate corners; anything within 20 px of the border is discarded, since a descriptor window would not fit there. Points are stored as $(y, x)$, i.e. (row, column).
3. **Strong-corner threshold.** Keep only corners with $h > 0.01\,\max h$.
4. **ANMS.** For each corner, find its suppression radius (below) and keep the 500 corners with the largest radius.

For corner $i$ with strength $f_i$, the suppression radius is the distance to the nearest corner that is clearly stronger:

$$
r_i=\min_j \|\mathbf{x}_i-\mathbf{x}_j\|\quad\text{s.t.}\quad f_i < c_{\text{robust}}\,f_j,\qquad c_{\text{robust}}=0.9.
$$

**Why the strong-corner threshold is needed.** Almost every textured pixel is a local maximum of $h$, so without a threshold the image is filled with corners: brick, floor tiles and noise all qualify. That buries the real corners, and it also makes ANMS slow because it compares every corner against every other one. The threshold leaves 14,860 corners on this photo.

**Why ANMS.** Even after the threshold, the strongest corners cluster on high-contrast spots (the lamps, the railings, the skylight). Taking the top 500 by strength would put most of them there. ANMS keeps a corner only if it is the strongest in a large neighborhood, so the 500 survivors are spread evenly across the image, which gives the homography much better coverage.

<div class="p3-grid c1">
  <figure>
    <img src="/images/cs180/proj4/output/B1/comparison.png" alt="Harris corners before and after ANMS on the HMMB atrium" loading="lazy">
    <figcaption>Left: 14,860 strong Harris corners. Right: the 500 corners kept by ANMS</figcaption>
  </figure>
</div>

## B.2: Feature Descriptor Extraction

#### Workflow

1. **Blur** the grayscale image with a Gaussian ($\sigma=2$) so subsampling does not alias.
2. **Window.** Take an axis-aligned $40\times40$ window centered on each ANMS corner.
3. **Subsample** every 5th pixel to get an $8\times8$ patch.
4. **Bias/gain normalize**: subtract the patch mean and divide by its standard deviation, so a brighter or higher-contrast copy of the same patch gives the same descriptor.
5. **Flatten** to a 64-dimensional vector for matching.

Sampling a large window coarsely makes the descriptor depend on the overall structure around a corner rather than on exact pixel values, so it tolerates small localization errors. My descriptors are single-scale and not rotation invariant, which is fine here because each pair is taken at roughly the same zoom and orientation.

The figure shows eight descriptors from the HMMB photo (ANMS with $n=8$). Titles are written as $(x, y)$ for readability even though the corners are stored as $(y, x)$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj4/output/B2/eight_descriptor.png" alt="Eight normalized 8x8 feature descriptors" loading="lazy">
    <figcaption>Eight normalized $8\times8$ descriptors, labeled by corner location $(x, y)$</figcaption>
  </figure>
</div>

## B.3: Feature Matching

#### Workflow

1. **Detect** 500 ANMS corners and their descriptors in each image (B.1 and B.2).
2. **Distance matrix.** Compute all $500\times500$ Euclidean distances between descriptors of image 1 and image 2 in one call, with no loops.
3. **Two nearest neighbors.** For each descriptor in image 1, find its closest ($d_1$) and second-closest ($d_2$) descriptor in image 2.
4. **Ratio test.** Accept the match only if $d_1/d_2<0.5$.

The ratio test comes from the observation in the MOPS paper (following Lowe) that $d_1$ alone separates correct and wrong matches poorly, but comparing it with $d_2$ works well. A correct match is much closer than any other candidate; a wrong one has a runner-up that is almost as close. A side effect is that repeated textures (windows, grids, railings) are mostly thrown out, because every copy looks alike and the ratio is near 1. On the HMMB pair this leaves 16 matches. We want the ratio to be less than the threshold instead of larger since smaller L2 distances among two image descriptors means they are very similar while large L2 distance means the two image descriptors are not similar. 

<div class="p3-grid c1">
  <figure>
    <img src="/images/cs180/proj4/output/B3/feature_match.png" alt="16 feature matches between the left and right HMMB photos" loading="lazy">
    <figcaption>Matches surviving the ratio test on the HMMB on the third floor pair</figcaption>
  </figure>
</div>

<div class="p3-grid c1">
  <figure>
    <img src="/images/cs180/proj4/output/B3/feature_match.png" alt="16 feature matches between the left and right HMMB photos" loading="lazy">
    <figcaption>Matches surviving the ratio test on the HMMB on the fourth floor pair</figcaption>
  </figure>
</div>

<div class="p3-grid c1">
  <figure>
    <img src="/images/cs180/proj4/output/B3/feature_match.png" alt="16 feature matches between the left and right HMMB photos" loading="lazy">
    <figcaption>Matches surviving the ratio test on the Wheeler Hall pair</figcaption>
  </figure>
</div>

## B.4: RANSAC for Robust Homography

#### Workflow

1. **Matched points** from B.3 are flipped from $(y, x)$ to $(x, y)$, since $H$ and the warp use $(x, y)$.
2. **Sample** 4 matches at random and compute the exact homography through them.
3. **Score** it: a match is an inlier if $H$ maps it within $\varepsilon=3$ px of its partner.
4. **Repeat** 2000 times and keep the largest inlier set.
5. **Refit** $H$ on all inliers with the normalized least squares from A.2.
6. **Warp and blend** with the bilinear warp and distance-transform feathering from A.4, at quarter resolution.

A single outlier can ruin a least-squares fit, but it can only ruin a RANSAC *sample*, and a bad sample simply gets few inliers and loses. Some samples are degenerate (nearly collinear points), so $H$ sends points to infinity and NumPy warns about a divide by zero. Those samples get zero inliers, so they are discarded without harm. One thing I found interesting is that running 2000 iterations at random is sufficient to find the best 4 point fit. We don't need to go through all possible combinations of picking 4 matching points. Probability theory guarantees we would get a good 4-point match in high chance. 

#### Manual vs. automatic

<div class="p3-grid c2">
  <figure>
    <img src="/images/cs180/proj4/output/A4/wheeler.jpg" alt="Wheeler mosaic from manual correspondences" loading="lazy">
    <figcaption>Wheeler, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/wheeler_auto.jpg" alt="Wheeler mosaic from automatic correspondences" loading="lazy">
    <figcaption>Wheeler, automatic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/moffitt.jpg" alt="Moffitt mosaic from manual correspondences" loading="lazy">
    <figcaption>Moffitt, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/moffit_auto.jpg" alt="Moffitt mosaic from automatic correspondences" loading="lazy">
    <figcaption>Moffitt, automatic</figcaption>
  </figure>
</div>

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Set</th><th>matches</th><th>inliers</th><th>mean error (auto / manual)</th><th>$\det H_{[1:2,1:2]}$ (auto / manual)</th></tr></thead>
  <tbody>
    <tr><td>Wheeler</td><td>93</td><td>82</td><td>1.03 / 0.34 px</td><td>1.45 / 1.46</td></tr>
    <tr><td>Moffitt</td><td>18</td><td>18</td><td>1.08 / 0.43 px</td><td>11.17 / 8.14</td></tr>
    <tr><td>HMMB 1</td><td>33</td><td>27</td><td>0.72 / 0.46 px</td><td>4.74 / 5.19</td></tr>
    <tr><td>HMMB 2</td><td>49</td><td>46</td><td>0.91 / 0.76 px</td><td>2.74 / 2.28</td></tr>
    <tr><td>Campanile</td><td>67</td><td>66</td><td>0.71 / 0.32 px</td><td>1.56 / 1.65</td></tr>
    <tr><td>Upper Sproul</td><td>52</td><td>51</td><td>0.57 / 0.42 px</td><td>1.80 / 1.83</td></tr>
    <tr><td>Sather Gate</td><td>90</td><td>88</td><td>0.73 / 0.34 px</td><td>1.14 / 1.09</td></tr>
  </tbody>
</table>

For Wheeler the automatic and manual homographies are nearly identical (determinant $1.45$ vs. $1.46$), and the mosaics are hard to tell apart. The automatic error is higher in every row and RANSAC accepts anything under 3 px by design.

Moffitt is the hard case. Only 18 matches survive, most likely because the ceiling is a repeating grid that the ratio test throws out. With so few points the warp is even stronger than the manual one ($11.2$ vs. $8.1$) and the right image is stretched further.

For both HMMB pairs, RANSAC rejects a few matches (6 of 33 and 3 of 49), which are the outliers the ratio test let through. The automatic and manual determinants are close, so both methods find a similar warp. These are pairs where I rotated my arm instead of the phone (see A.1). Automatic matching cannot fix that problem, because the parallax is in the photos themselves.

<div class="p3-grid c2">
  <figure>
    <img src="/images/cs180/proj4/output/A4/not_so_good/hmmb_blend.jpg" alt="HMMB 1 mosaic from manual correspondences" loading="lazy">
    <figcaption>HMMB 1, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/hearst_auto.jpg" alt="HMMB 1 mosaic from automatic correspondences" loading="lazy">
    <figcaption>HMMB 1, automatic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/not_so_good/hmmb_2_blend.jpg" alt="HMMB 2 mosaic from manual correspondences" loading="lazy">
    <figcaption>HMMB 2, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/hearst_2_auto.jpg" alt="HMMB 2 mosaic from automatic correspondences" loading="lazy">
    <figcaption>HMMB 2, automatic</figcaption>
  </figure>
</div>

<div class="p3-grid c4">
  <figure>
    <img src="/images/cs180/proj4/images/B/hearst_left.jpg" alt="HMMB 1 left source" loading="lazy">
    <figcaption>HMMB 1, source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/B/hearst_right.jpg" alt="HMMB 1 right source" loading="lazy">
    <figcaption>HMMB 1, source (right)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/B/hearst_2_left.jpg" alt="HMMB 2 left source" loading="lazy">
    <figcaption>HMMB 2, source (left)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/images/B/hearst_2_right.jpg" alt="HMMB 2 right source" loading="lazy">
    <figcaption>HMMB 2, source (right)</figcaption>
  </figure>
</div>

#### More automatic mosaics: campus photos

On the campus pairs from the next morning, almost every match survives RANSAC (66 of 67, 51 of 52 and 88 of 90), and the automatic determinants are within a few percent of the manual ones. These photos have plenty of distinct corners and little repeated texture, so the pipeline matches my hand clicks without any effort.

<div class="p3-grid c2">
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/campus_photos/tower.jpg" alt="Campanile mosaic from manual correspondences" loading="lazy">
    <figcaption>Campanile, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/tower_auto.jpg" alt="Campanile mosaic from automatic correspondences" loading="lazy">
    <figcaption>Campanile, automatic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/campus_photos/upper_sproul.jpg" alt="Upper Sproul mosaic from manual correspondences" loading="lazy">
    <figcaption>Upper Sproul, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/plaza_auto.jpg" alt="Upper Sproul mosaic from automatic correspondences" loading="lazy">
    <figcaption>Upper Sproul, automatic</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/A4/extras/campus_photos/sather_gate.jpg" alt="Sather Gate mosaic from manual correspondences" loading="lazy">
    <figcaption>Sather Gate, manual (Part A)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj4/output/B4/gate_auto.jpg" alt="Sather Gate mosaic from automatic correspondences" loading="lazy">
    <figcaption>Sather Gate, automatic</figcaption>
  </figure>
</div>

## Conclusion

The most important thing I learned is that the math is only half of the project; the data matters just as much. A homography assumes a pure rotation about the camera center, and when I rotated my arm instead of the camera, no amount of careful least squares could fix the mosaics. On the math side, normalizing the points reduced $\kappa(A)$ from about $10^{9}$ to about $4$ and halved the reprojection error, and the determinant of $H$ and $\kappa(A)$ from EECS 127 turned out to be quick ways to judge whether a recovered system is trustworthy. Inverse warping avoids holes, bilinear interpolation gives much cleaner results than nearest neighbor for a small cost, and distance-transform feathering gives a simple, effective way to blend.

Part B showed that clicking is not needed at all. A simple, hand-designed pipeline of Harris corners, ANMS, $8\times8$ patches, a ratio test and RANSAC is enough for automatic stitching, with no neural networks and no training data. Each step is easy to understand on its own. Its weak spot is repeated texture, where the ratio test leaves too few matches, as in Moffitt.