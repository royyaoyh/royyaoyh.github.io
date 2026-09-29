---
title: "Project 2: Fun with Filters and Frequencies!"
collection: cs180
permalink: /cs180/project_2/
date: 2026-09-28
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
/* ---- Project 2 page styles (scoped with p2- prefixes) ---- */
.page__title, h1.page__title, article h1, .page__content h1 { font-size: 2.4em !important; }
.page__content h2, article h2 { font-size: 1.8em !important; }
.page__content h3, article h3 { font-size: 1.45em !important; margin-top: 2em; }
.page__content h4, article h4 { font-size: 1.2em !important; margin-top: 1.6em; }
.p2-grid { --n: 3; --g: 14px; display: flex; flex-wrap: wrap; justify-content: center; align-items: flex-start; gap: var(--g); margin: 1.4em auto 0.6em; }
.p2-grid figure { display: block; margin: 0; min-width: 0; text-align: center; flex: 0 0 calc((100% - (var(--n) - 1) * var(--g)) / var(--n)); }
.p2-grid figure.w2 { flex-basis: calc(2 * (100% - (var(--n) - 1) * var(--g)) / var(--n) + var(--g)); }
.p2-grid img { display: block; width: 100%; height: auto; margin: 0; border-radius: 3px; }
.p2-grid figcaption { margin: 0.45em 0 0; font-size: 0.85em; font-style: italic; line-height: 1.35; color: inherit; opacity: 0.85; text-align: center; }
.p2-grid.c1 { --n: 1; }
.p2-grid.c2 { --n: 2; }
.p2-grid.c3 { --n: 3; }
.p2-grid.c4 { --n: 4; }
.p2-grid.narrow { max-width: 640px; }
.p2-grid.medium { max-width: 820px; }
.p2-cap { text-align: center; font-style: italic; font-size: 0.9em; color: inherit; opacity: 0.85; margin: 0.2em 0 1.6em; }
.p2-table { width: auto !important; display: table !important; margin: 1em auto 1.4em !important; border-collapse: collapse; }
.p2-table th, .p2-table td { text-align: center !important; padding: 0.45em 1.8em; white-space: nowrap; }
.p2-note { color: inherit; border-left: 4px solid #6f97c9; background: rgba(111, 151, 201, 0.14); padding: 0.7em 1.1em; margin: 1.4em 0; border-radius: 0 4px 4px 0; }
.p2-note p:last-child, .p2-note ol:last-child { margin-bottom: 0; }
.p2-note ol { margin-top: 0.4em; }
.p2-code { margin: 1.2em 0; border: 1px solid rgba(128, 128, 128, 0.45); border-radius: 6px; overflow: hidden; color: inherit; }
.p2-code-title { padding: 0.5em 1em; font-weight: 700; color: inherit; background: rgba(128, 128, 128, 0.14); border-bottom: 1px solid rgba(128, 128, 128, 0.35); }
.p2-code > p { margin: 0; padding: 0.6em 1em; }
.p2-code .highlighter-rouge, .p2-code .highlight, .p2-code pre { margin: 0 !important; border: 0 !important; border-radius: 0 !important; box-shadow: none !important; overflow-x: auto; }
.p2-code pre { padding: 0.8em 1em !important; }
html .p2-code .highlighter-rouge, html .p2-code .highlight, html .p2-code pre, html .p2-code pre code { background: #ffffff !important; color: #24292f !important; }
html .p2-code .highlight span { color: #24292f !important; background: transparent !important; }
html .p2-code .highlight span[class^="c"] { color: #6e7781 !important; font-style: italic; }
html .p2-code .highlight span[class^="k"], html .p2-code .highlight span.ow { color: #cf222e !important; }
html .p2-code .highlight span[class^="s"] { color: #0a3069 !important; }
html .p2-code .highlight span[class^="m"], html .p2-code .highlight span.il { color: #0550ae !important; }
html .p2-code .highlight span.nf, html .p2-code .highlight span.nc, html .p2-code .highlight span.fm { color: #8250df !important; }
html .p2-code .highlight span.nb, html .p2-code .highlight span.bp { color: #0550ae !important; }
html[data-p2-mode="dark"] .p2-code .highlighter-rouge, html[data-p2-mode="dark"] .p2-code .highlight, html[data-p2-mode="dark"] .p2-code pre, html[data-p2-mode="dark"] .p2-code pre code { background: #0d1117 !important; color: #e6edf3 !important; }
html[data-p2-mode="dark"] .p2-code .highlight span { color: #e6edf3 !important; background: transparent !important; }
html[data-p2-mode="dark"] .p2-code .highlight span[class^="c"] { color: #8b949e !important; font-style: italic; }
html[data-p2-mode="dark"] .p2-code .highlight span[class^="k"], html[data-p2-mode="dark"] .p2-code .highlight span.ow { color: #ff7b72 !important; }
html[data-p2-mode="dark"] .p2-code .highlight span[class^="s"] { color: #a5d6ff !important; }
html[data-p2-mode="dark"] .p2-code .highlight span[class^="m"], html[data-p2-mode="dark"] .p2-code .highlight span.il { color: #79c0ff !important; }
html[data-p2-mode="dark"] .p2-code .highlight span.nf, html[data-p2-mode="dark"] .p2-code .highlight span.nc, html[data-p2-mode="dark"] .p2-code .highlight span.fm { color: #d2a8ff !important; }
html[data-p2-mode="dark"] .p2-code .highlight span.nb, html[data-p2-mode="dark"] .p2-code .highlight span.bp { color: #79c0ff !important; }
.p2-cmp { display: block !important; max-width: 640px; margin: 1.4em auto 0.6em !important; text-align: center; }
.p2-cmp-stage { position: relative; overflow: hidden; line-height: 0; border-radius: 4px; touch-action: pan-y; -webkit-user-select: none; user-select: none; cursor: ew-resize; }
.p2-cmp-stage img { display: block; width: 100%; height: auto; margin: 0 !important; pointer-events: none; -webkit-user-drag: none; }
.p2-cmp-stage img.p2-layer { position: absolute; top: 0; left: 0; height: 100%; object-fit: cover; }
.p2-cmp-tag { position: absolute; top: 0; overflow: hidden; pointer-events: none; text-align: left; }
.p2-cmp-tag span { display: inline-block; max-width: calc(100% - 16px); margin: 8px; padding: 2px 8px; font: 600 0.75rem/1.6 sans-serif; color: #fff; background: rgba(0, 0, 0, 0.6); border-radius: 3px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; vertical-align: top; }
.p2-cmp-handle { position: absolute; top: 0; bottom: 0; width: 44px; margin-left: -22px; cursor: ew-resize; touch-action: pan-y; outline: none; }
.p2-cmp-handle::before { content: ""; position: absolute; top: 0; bottom: 0; left: 50%; width: 2px; margin-left: -1px; background: #e8973a; }
.p2-cmp-knob { position: absolute; top: 50%; left: 50%; width: 34px; height: 34px; margin: -17px 0 0 -17px; border-radius: 50%; background: #e8973a; color: #111; font: 700 16px/34px sans-serif; letter-spacing: -1px; text-align: center; box-shadow: 0 1px 6px rgba(0, 0, 0, 0.45); }
.p2-cmp-handle:focus-visible .p2-cmp-knob { box-shadow: 0 0 0 3px rgba(232, 151, 58, 0.55); }
.p2-cmp figcaption { margin-top: 0.5em; font-size: 0.9em; font-style: italic; line-height: 1.4; color: inherit; opacity: 0.85; text-align: center; }
@media (max-width: 560px) { .p2-grid.c3, .p2-grid.c4 { --n: 2; } }
</style>

<script>
(function () {
  function lum(c) { var m = c.match(/[\d.]+/g); if (!m || (m.length > 3 && parseFloat(m[3]) === 0)) { return null; } return (0.299 * m[0] + 0.587 * m[1] + 0.114 * m[2]) / 255; }
  function pageIsDark() {
    var els = [document.querySelector('.page__content'), document.querySelector('article'), document.body, document.documentElement];
    for (var i = 0; i < els.length; i++) { if (els[i]) { var l = lum(getComputedStyle(els[i]).backgroundColor); if (l !== null) { return l < 0.5; } } }
    return window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
  }
  function apply() { document.documentElement.setAttribute('data-p2-mode', pageIsDark() ? 'dark' : 'light'); }
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
    stage.className = 'p2-cmp-stage';
    fig.insertBefore(stage, imgs[0]);
    var pos = [], tags = [], handles = [], k;
    for (k = 0; k < n; k++) {
      stage.appendChild(imgs[k]);
      if (k > 0) { imgs[k].className += ' p2-layer'; pos.push(100 * k / n); }
    }
    for (k = 0; k < n; k++) {
      var box = document.createElement('div'), chip = document.createElement('span');
      box.className = 'p2-cmp-tag';
      chip.textContent = labels[k] || '';
      box.appendChild(chip); stage.appendChild(box); tags.push(box);
    }
    for (k = 0; k < n - 1; k++) {
      var h = document.createElement('div');
      h.className = 'p2-cmp-handle';
      h.setAttribute('role', 'slider'); h.setAttribute('tabindex', '0');
      h.setAttribute('aria-label', 'Comparison slider ' + (k + 1));
      h.setAttribute('aria-valuemin', '0'); h.setAttribute('aria-valuemax', '100');
      h.innerHTML = '<span class="p2-cmp-knob">&lsaquo;&rsaquo;</span>';
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
  function run() { Array.prototype.forEach.call(document.querySelectorAll('.p2-cmp'), init); }
  if (document.readyState === 'loading') { document.addEventListener('DOMContentLoaded', run); } else { run(); }
})();
</script>

## Overview

This project is about understanding images as 2D signals and seeing how convolution and frequency filtering can be used for edges, sharpening, hybrid images, and seamless blending. I started from implementing convolution myself, then used derivatives and Gaussian smoothing to build better edge detectors. In the second half, I worked with high and low spatial frequencies to sharpen images, create hybrid images, and finally blend different images together with Gaussian and Laplacian stacks.

## Part 1: Fun with Filters

### Part 1.1: Convolutions from Scratch!

#### 4 loops

We loop through the image pixel by pixel, then we iterate through the kernel. In the four-loop version, the outer two loops choose the output pixel location, while the inner two loops iterate over every element of the kernel. For each output pixel, I multiply the overlapping image values by the corresponding kernel values and add them together.

The basic operation can be written as

$$
O(i,j) = \sum_{m}\sum_{n} I(i-m,j-n)K(m,n),
$$

where $I$ is the image, $K$ is the filter, and $O$ is the output. The filter is flipped before applying the operation because convolution, under the strict mathematical convention, requires reversing the kernel in both dimensions.

<div class="p2-code" markdown="1">

<div class="p2-code-title">Code: 4-loop convolution</div>

```python
# 4 loops
def convolve2d_4loops(image, ker):
    img_rows, img_cols = image.shape
    # flip the kernal
    kernel = np.flip(ker)

    k_rows, k_cols = kernel.shape

    center_m = k_rows // 2
    center_n = k_cols // 2

    output = np.zeros((img_rows, img_cols))
    height = output.shape[0]
    width = output.shape[1]
    
    # iterate image rows
    for i in range(height):
        # iterate image columns
        for j in range(width):
            sum_val = 0.0
            # iterate kernal rows
            for m in range(k_rows):
                # iterate kernal columns
                for n in range(k_cols):
                    row = i + m - center_m
                    col = j + n - center_n
                    if 0 <= row < img_rows and 0 <= col < img_cols:
                        sum_val += image[row, col] * kernel[m, n]
            output[i, j] = sum_val
            
    return output
```

</div>

#### 2 loops

2 loops we iterate through the image pixel by pixel, write an edge case handler that we only need to sum the multiplication the sliced 2d kernel with the sliced 2d window

Instead of explicitly looping over the kernel elements, I can use a sliced portion of the image and kernel and perform the element-by-element multiplication followed by `np.sum`. This keeps the two loops for the image coordinates while using NumPy for the inner matrix operation. Near an image boundary, the kernel extends outside the image and get index out of bound error, so I use an edge-case handler that only multiplies the overlapping portions.

Conceptually, for a kernel centered at $(i,j)$, I find the valid image window and the matching valid portion of the kernel. The output pixel is then

$$
O(i,j)=\sum_{(m,n)\in \text{valid overlap}} I(m,n)K'(m,n),
$$

where $K'$ is the flipped kernel. Using zero padding, the part of the kernel outside the image is equivalent to multiplying by zeros.

<div class="p2-code" markdown="1">

<div class="p2-code-title">Code: 2-loop convolution</div>

```python
# 2 loops

def convolve_2d_2loops(image, ker):
    img_rows, img_cols = image.shape
    # flip the kernal
    kernel = np.flip(ker)

    k_rows, k_cols = kernel.shape

    center_m = k_rows // 2
    center_n = k_cols // 2

    output = np.zeros((img_rows, img_cols))
    height = output.shape[0]
    width = output.shape[1]
    
    # iterate image rows
    for i in range(height):
        # iterate image columns
        for j in range(width):
            # pick the width and height range on the image
            r_start = max(0, i - center_m)
            r_end = min(img_rows, i + center_m + 1)

            c_start = max(0, j - center_n)
            c_end = min(img_cols, j + center_n + 1)

            kr_start = r_start - (i - center_m)
            kr_end = kr_start + (r_end - r_start)

            kc_start = c_start - (j - center_n)
            kc_end = kc_start + (c_end - c_start)

            output[i, j] = np.sum(
                image[r_start:r_end, c_start:c_end] *
                kernel[kr_start:kr_end, kc_start:kc_end]
            )

    return output
```
</div>

#### What can you use for this section?

I am not entirely sure about this question. I did use np.flip to flip the kernel and use np.sum in the 2d loops for the element by element matrix multiplication.

I used NumPy operations only for my from-scratch implementations. `np.flip` handles the kernel reversal needed for convolution, while `np.sum` computes the sum of all element-wise products in the overlapping region. This is the main difference between my four-loop and two-loop approaches: both perform the same convolution mathematically, but the two-loop version lets NumPy handle the inner arithmetic.

#### Runtime analysis

**4 loops**, timed on the horizontal edge detector, the vertical edge detector, and the box filter:

<table class="p2-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Filter</th><th>Runtime (s)</th></tr></thead>
  <tbody>
    <tr><td>Horizontal edge detector</td><td>23.0980</td></tr>
    <tr><td>Vertical edge detector</td><td>25.6154</td></tr>
    <tr><td>Box filter</td><td>16.6827</td></tr>
  </tbody>
</table>

The average runtime of these three four-loop runs is about $21.80$ seconds. The box filter is faster in this particular test, but the important comparison is between the implementation strategies rather than between the three filters themselves.

**2 loops**, timed on the same three filters:

<table class="p2-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Filter</th><th>Runtime (s)</th></tr></thead>
  <tbody>
    <tr><td>Horizontal edge detector</td><td>16.1894</td></tr>
    <tr><td>Vertical edge detector</td><td>16.1046</td></tr>
    <tr><td>Box filter</td><td>15.9178</td></tr>
  </tbody>
</table>

The average runtime drops to about $16.07$ seconds. This is about $$21.8\,/\,16.1 \approx 1.36\times$$ faster than the four-loop implementation in my measurements. The mathematical operation is the same, but using NumPy's array operations avoids executing the innermost arithmetic directly in Python.

**Library `convolve2d`**, timed on the same three filters:

<table class="p2-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Filter</th><th>Runtime (s)</th></tr></thead>
  <tbody>
    <tr><td>Horizontal edge detector</td><td>0.0749</td></tr>
    <tr><td>Vertical edge detector</td><td>0.0730</td></tr>
    <tr><td>Box filter</td><td>0.0824</td></tr>
  </tbody>
</table>

The library implementation is dramatically faster, with an average of about $0.0767$ seconds. Compared with my two-loop implementation, the measured speedup is roughly $16.07/0.0767 \approx 209\times$. This illustrates why optimized numerical libraries are useful for image processing: the operation itself is simple, but doing billions of small Python-level operations is expensive.

#### Results

<div class="p2-grid c4">
  <figure>
    <img src="/images/cs180/proj2/image/me_grayscale.jpg" alt="Input grayscale image" loading="lazy">
    <figcaption>Me at Chinatown</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_1/me_gray_box.jpg" alt="9×9 box filter" loading="lazy">
    <figcaption>9×9 Box Filter</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_1/me_gray_hor.jpg" alt="Horizontal derivative" loading="lazy">
    <figcaption>Horizontal Derivative</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_1/me_gray_ver.jpg" alt="Vertical derivative" loading="lazy">
    <figcaption>Vertical Derivative</figcaption>
  </figure>
</div>

<p class="p2-cap">Self image with box and finite-difference filters</p>

The box filter averages a local neighborhood. For a $9\times9$ box filter, each coefficient is

$$
K_{box}(m,n)=\frac{1}{81},
$$

so the filter replaces each pixel with the average of the $81$ values in its neighborhood. This smooths the image and removes some fine detail.

The derivative filters instead respond to changes between neighboring pixels. The horizontal and vertical outputs therefore emphasize different edge orientations. This also demonstrates an important distinction between smoothing and differentiation: the box filter averages nearby values, while the derivative filters measure local changes.

<div class="p2-note" markdown="1">

**Keynote**

1. We need to center the filter so that the center of the filter is at the pixel we are looking at. and flip the filter to achieve convolution (using the strict mathematical convention)
2. We do convolution thats the same as `convolve2d` with `mode="same"`. Here we did zero padding

</div>

For the boundary, `mode="same"` keeps the output the same size as the input while still computing a result near the edge. My implementation uses zero padding, so values outside the image are treated as zero. This matters because the kernel is only partially supported near the border, which can make edge pixels look different from interior pixels.

### Part 1.2: Finite Difference Operator

I used `im = np.hypot(ver, hor)` to get the magnitude of the horizontal and vertical derivative

<div class="p2-grid c2 narrow">
  <figure>
    <img src="/images/cs180/proj2/output/supplement/cameraman_hor.png" alt="D_x: horizontal derivative" loading="lazy">
    <figcaption>$D_x$: Horizontal Derivative</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/supplement/cameraman_ver.png" alt="D_y: vertical derivative" loading="lazy">
    <figcaption>$D_y$: Vertical Derivative</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_2/cameraman_raw_edge.png" alt="Gradient magnitude before thresholding" loading="lazy">
    <figcaption>Gradient Magnitude Before Thresholding</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_2/cameraman_thres.png" alt="Binarized edge image" loading="lazy">
    <figcaption>Binarized Edge Image</figcaption>
  </figure>
</div>

The finite-difference operators estimate the partial derivatives of image intensity. Using

$$
D_x = \begin{bmatrix}-1 & 0 & 1\end{bmatrix},
\qquad
D_y = \begin{bmatrix}-1\\0\\1\end{bmatrix},
$$

I obtain the horizontal and vertical derivative images by convolution. The gradient magnitude combines them as

$$
G = \sqrt{D_x^2 + D_y^2}.
$$

In my implementation, `np.hypot(ver, hor)` computes exactly this combination while avoiding manually writing the square-root expression.

The result is useful because an edge is a location where image intensity changes quickly in that direction. A large derivative means a strong local change, and the gradient magnitude measures the overall strength of that change regardless of whether it is mainly horizontal or vertical.

Finally, I binarize the gradient magnitude by selecting a threshold $T$:

$$
E(i,j)=
\begin{cases}
1,&G(i,j)>T\\
0,&G(i,j)\le T.
\end{cases}
$$

The threshold is a qualitative tradeoff. A threshold that is too low keeps small changes and noise, while a threshold that is too high removes weak but real edges. I chose the threshold to suppress much of the noise while retaining the main edges in the cameraman image.

<div class="p2-grid c2 narrow">
  <figure>
    <img src="/images/cs180/proj2/output/supplement/cameraman_gauss_thres.png" alt="Binarized gradient magnitude after Gaussian smoothing" loading="lazy">
    <figcaption>Binarized Edge Image After Gaussian Smoothing</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_2/cameraman_thres.png" alt="Finite-difference binarized edge image" loading="lazy">
    <figcaption>Finite-difference Binarized Edge Image</figcaption>
  </figure>
</div>

<p class="p2-cap">Comparison of binarized edge maps before and after Gaussian smoothing. You see the binarized edge image with Gaussian Smoothing have less noise on oblique edges</p>

### Part 1.3: Derivative of Gaussian (DoG) Filter

I used the following to create a 2D Gaussian kernel:

```python
kernel_1d = cv.getGaussianKernel(5, 1.0)
kernel_2d = kernel_1d * kernel_1d.T
```

The 2D Gaussian is separable, so taking the outer product of the 1D Gaussian vector with its transpose gives the full 2D filter. In general,

$$
G(x,y)=G_x(x)G_y(y),
$$

which is why the outer product works.

A Gaussian filter is a low-pass filter: it reduces rapid pixel-to-pixel changes before the derivative is taken. This means that small noisy variations are less likely to appear as strong edges.

The key mathematical idea is associativity of convolution. If $I$ is the image, $G$ is the Gaussian filter, and $D_x$ is the derivative filter, then

$$
D_x * (G * I) = (D_x * G) * I.
$$

The same applies in the vertical direction:

$$
D_y * (G * I) = (D_y * G) * I.
$$

Therefore, instead of first blurring the image and then applying the derivative, I can first combine the Gaussian and derivative filters into a single filter and convolve that result directly with the original image. This combined filter is the Derivative of Gaussian (DoG) filter.

<div class="p2-grid c2 narrow">
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss_filtered_hor.png" alt="Gaussian + horizontal derivative" loading="lazy">
    <figcaption>Gaussian + Horizontal Derivative</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss_filtered_ver.png" alt="Gaussian + vertical derivative" loading="lazy">
    <figcaption>Gaussian + Vertical Derivative</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_3/gauss_hor_kernal.png" alt="Horizontal DoG filter" loading="lazy">
    <figcaption>Horizontal DoG Filter</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_3/gauss_ver_kernal.png" alt="Vertical DoG filter" loading="lazy">
    <figcaption>Vertical DoG Filter</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss.png" alt="Gaussian-smoothed cameraman" loading="lazy">
    <figcaption>Gaussian-smoothed Cameraman</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss_filtered_filter.png" alt="Gradient magnitude from the DoG filters" loading="lazy">
    <figcaption>Gradient Magnitude from the DoG filters</figcaption>
  </figure>
</div>

The two methods provide the same images except on the edges due to zero padding being applied once vs. being applied twice.

The small differences around the borders are expected because the two implementations can expose the image boundary to padding at different stages. In the interior of the image, the associativity relationship predicts the same filtering result, while boundary handling can make the final pixels differ.

Compared with the original finite-difference result, the Gaussian-smoothed result should have fewer small noisy responses. The tradeoff is that very fine detail can also be removed because the Gaussian is intentionally suppressing high spatial frequencies before differentiation.

<div class="p2-note" markdown="1">

**Keynote:** When convolving the kernel with the Gaussian filter, make sure to use `mode="full"` to not lose filter details at the corner.

Using a full convolution when constructing the combined DoG kernel preserves the complete support of the two filters before they are applied to the image. The resulting combined filter is larger than either filter alone because the convolution of two finite kernels increases their support.

</div>

The Gaussian-smoothed edge image is cleaner than the finite-difference edge image because smoothing reduces small high-frequency variations before differentiation. The main edges remain, while many weaker responses are suppressed. This is the main practical advantage I observe from adding the Gaussian filter before the derivative.

## Part 2: Fun with Frequencies

### Part 2.1: Image "Sharpening"

In high level, sharpening works by separating an image into a low-frequency part and a high-frequency part. A blurred image keeps mostly low-frequency information, while the difference between the original and blurred image captures high-frequency details such as edges and fine texture.

I used the unsharp-mask idea to add some of that high-frequency information back into the image. It disproportionally amplifies the high frequency components of an image while making the low-frequency component the same. This gives sharp constant on edges.

A useful way to write the process is

$$
L = G_\sigma * I,
$$

where $L$ is the Gaussian-blurred, low-frequency image. The high-frequency component is

$$
H = I - L.
$$

Then the sharpened image is

$$
I_{sharp}=I+\alpha H
=I+\alpha(I-G_\sigma * I),
$$

where $\alpha$ controls the amount of sharpening. Equivalently,

$$
I_{sharp}=(1+\alpha)I-\alpha(G_\sigma*I),
$$

which shows that the entire operation can be represented as a single convolution with an unsharp-mask filter.

<div class="p2-grid c4">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_1/taj_0.5.jpg" alt="Taj, alpha=0.5" loading="lazy">
    <figcaption>Taj, $\alpha=0.5$</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_1/taj_1.jpg" alt="Taj, alpha=1" loading="lazy">
    <figcaption>Taj, $\alpha=1$</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_1/taj_2.jpg" alt="Taj, alpha=2" loading="lazy">
    <figcaption>Taj, $\alpha=2$</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_1/taj_5.jpg" alt="Taj, alpha=5" loading="lazy">
    <figcaption>Taj, $\alpha=5$</figcaption>
  </figure>
</div>

<p class="p2-cap">Effect of increasing the sharpening amount</p>

As the sharpening amount increases from $\alpha=0.5$ to $\alpha=5$, the high-frequency component contributes more strongly to the final image. The lower values make a subtler change, while the larger values emphasize fine detail much more aggressively. This provides a direct visual demonstration of the role of $\alpha$ in the equation above.

<div class="p2-grid c3">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_1/taj.jpg" alt="Original Taj Mahal" loading="lazy">
    <figcaption>Original Taj Mahal</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/supplement/taj_blur.jpg" alt="Taj, Gaussian blurred" loading="lazy">
    <figcaption>Taj, Gaussian Blurred</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/supplement/taj_high_freq.jpg" alt="Taj, high-frequency component" loading="lazy">
    <figcaption>Taj, High-frequency Component</figcaption>
  </figure>
</div>

<p class="p2-cap">Decomposing the Taj Mahal image into low and high spatial frequencies</p>

<figure class="p2-cmp" data-labels="Original|Blurred, then sharpened|Sharpened">
  <img src="/images/cs180/proj2/image/Part_2_1/lobos.jpg" alt="Point Lobos, original">
  <img src="/images/cs180/proj2/output/Part_2_1/lobos_blur_5.jpg" alt="Point Lobos, blurred then sharpened">
  <img src="/images/cs180/proj2/output/Part_2_1/lobos_5.jpg" alt="Point Lobos, sharpened">
  <figcaption>Point Lobos: drag the two handles to compare the original, the blurred-then-sharpened image, and the sharpened image</figcaption>
</figure>

#### For evaluation, pick a sharp image, blur it, and then try to sharpen it again. Compare the original and the sharpened image and report your observations.

<p class="p2-cap">Sharp → blur → sharpen-back experiment on Lobos</p>

The images are the direct evaluation requested in the project description: I start with the original sharp image, blur it, and then apply the sharpening operation to that blurred image. The sharpened result recovers some apparent edge contrast, but it does not become identical to the original because the blur has already removed high-frequency information.

After subtracting the original image by the sharpened blurred image, the original image have more high frequency information than the sharpened blurred image. This can be caused by the lost high-frequency information that cannot be recovered after the blur. Applying the sharpening mask boosts the high-frequency details remaining in the blurred image, but it can't recover the information that was lost.

This experiment shows an important limitation of sharpening. Sharpening is not a time machine: once the blur has removed high-frequency information, the missing information cannot be exactly reconstructed. The sharpening filter only increases the contrast of the high-frequency information that remains after blurring. As $\alpha$ increases, edges become more pronounced, but overly large values can also make noise more visible.

<div class="p2-note" markdown="1">

**Keynote:** always do `np.clip` or scale the image to the range from 0 to 1 before converting the floating points ot integers and multiplying by 255. This would cause integer overflow for example, a -1 value would be 255, which would add a lot of random color and pepper noise

</div>

Because the sharpening equation can produce values below $0$ or above $1$, the result needs to be brought back into the valid image range before converting to `uint8`. Otherwise, negative values or values above the maximum can wrap or clip incorrectly during integer conversion. Using `np.clip(res,0,1)` before multiplying by $255$ is a safe way to handle this.

#### More sharpening examples

Drag the handle on each image to compare the original (left) with the sharpened result (right).

<figure class="p2-cmp" data-labels="Original|Sharpened">
  <img src="/images/cs180/proj2/image/Part_2_1/calacademy.jpg" alt="California Academy of Sciences, original">
  <img src="/images/cs180/proj2/output/Part_2_1/calacademy_5.jpg" alt="California Academy of Sciences, sharpened">
  <figcaption>California Academy of Sciences</figcaption>
</figure>

<figure class="p2-cmp" data-labels="Original|Sharpened">
  <img src="/images/cs180/proj2/image/Part_2_1/redwood.jpg" alt="Henry Cowell Redwoods State Park, original">
  <img src="/images/cs180/proj2/output/Part_2_1/redwood_5.jpg" alt="Henry Cowell Redwoods State Park, sharpened">
  <figcaption>Henry Cowell Redwoods State Park</figcaption>
</figure>

<figure class="p2-cmp" data-labels="Original|Sharpened">
  <img src="/images/cs180/proj2/image/Part_2_1/yosemite.jpg" alt="Yosemite National Park, original">
  <img src="/images/cs180/proj2/output/Part_2_1/yosemite_5.jpg" alt="Yosemite National Park, sharpened">
  <figcaption>Yosemite National Park</figcaption>
</figure>

### Part 2.2: Hybrid Images

In high level, we combine one image’s high-frequency information wth another image’s low-frequency’s information. With some alignment, we can get some bizarre and funny results.

The basic construction is

$$
I_{hybrid}=H_{image\ 1}+L_{image\ 2},
$$

where

$$
H_{image\ 1}=I_1-G_{\sigma_h}*I_1
$$

and

$$
L_{image\ 2}=G_{\sigma_l}*I_2.
$$

The alignment is important because the high-frequency structure needs to correspond spatially with the low-frequency structure. When the two images are aligned well, the visual system can interpret different information depending on viewing distance.

#### Cutoff frequency choice

- **High frequency:** increase sigma → more information, decrease sigma → less information
- **Low frequency:** increase sigma → less information, decrease sigma → more information

The intuition is that $\sigma$ controls the width of the Gaussian blur. A larger $\sigma$ removes more high frequencies from the low-pass image, which makes the low-pass result smoother. For the high-pass component $$I-G_\sigma*I$$, a larger $\sigma$ means more of the original image is treated as high-frequency detail because the blur removes a wider range of frequencies.

Based on this rule, I tuned it that whenever I have my glass on, its the high frequency image while if I have my glass off, its the low frequency image. (Roy’s criteria)

This criterion is based on the expected viewing distance. At close range, the fine details are available and the high-frequency image is easier to recognize. At farther distances, the image is effectively blurred by the limited resolution of the visual system, making the low-frequency interpretation more prominent.

<div class="p2-grid c4">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_2/cat.jpg" alt="Cat input" loading="lazy">
    <figcaption>Cat</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_2/man.jpg" alt="Man input" loading="lazy">
    <figcaption>Man</figcaption>
  </figure>
  <figure class="w2">
    <img src="/images/cs180/proj2/output/Part_2_2/catman.png" alt="Cat + man hybrid" loading="lazy">
    <figcaption>Cat + Man Hybrid</figcaption>
  </figure>
</div>

<p class="p2-cap">Hybrid Result For the Cat/Man Pair</p>

<div class="p2-grid c4">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/seal.png" alt="Seal" loading="lazy">
    <figcaption>Bagua Diagram</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/bagua.png" alt="Bagua" loading="lazy">
    <figcaption>Berkeley Seal</figcaption>
  </figure>
  <figure class="w2">
    <img src="/images/cs180/proj2/output/Part_2_2/seal_bagua.png" alt="Seal + Bagua Diahybrid" loading="lazy">
    <figcaption>Berkeley Seal + Bagua Digram Hybrid</figcaption>
  </figure>
</div>

<div class="p2-grid c4">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_2/lian_headshot.png" alt="Lian" loading="lazy">
    <figcaption><a href="https://space.bilibili.com/1437582453" target="_blank" rel="noopener">Azuma Seren</a> VTuber</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_2/taffy_headshot.png" alt="Taffy" loading="lazy">
    <figcaption><a href="https://space.bilibili.com/1265680561" target="_blank" rel="noopener">Ace Taffy</a> VTuber</figcaption>
  </figure>
  <figure class="w2">
    <img src="/images/cs180/proj2/output/Part_2_2/taffy_lian.png" alt="Taffy + Lian hybrid" loading="lazy">
    <figcaption><a href="https://space.bilibili.com/1265680561" target="_blank" rel="noopener">Ace Taffy</a> + <a href="https://space.bilibili.com/1437582453" target="_blank" rel="noopener">Azuma Seren</a> Hybrid</figcaption>
  </figure>
</div>

<div class="p2-grid c4">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_2/hu_ge.png" alt="Hu Ge" loading="lazy">
    <figcaption>Brother Tiger</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_2/pig.png" alt="Pig" loading="lazy">
    <figcaption>Pig</figcaption>
  </figure>
  <figure class="w2">
    <img src="/images/cs180/proj2/output/Part_2_2/hu_pig.png" alt="Hu Ge + pig hybrid" loading="lazy">
    <figcaption>Brother Tiger + Pig Hybrid</figcaption>
  </figure>
</div>

These examples demonstrate how changing the input pair changes the visual effect, while the same high-pass/low-pass construction remains the same. The cat/man result is especially useful for showing the intended distance-dependent interpretation, while the other examples show that the technique is not limited to a single type of subject.

#### Full frequency analysis example

I use the <a href="https://space.bilibili.com/1437582453" target="_blank" rel="noopener">Azuma Seren</a>/<a href="https://space.bilibili.com/1265680561" target="_blank" rel="noopener">Ace Taffy</a> pair as the main worked example. Here are the aligned inputs, their Fourier magnitudes, and the spectra of the high-pass, low-pass, and hybrid images.

<div class="p2-grid c2 narrow">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/aligned_lian.png" alt="Aligned Azuma Seren" loading="lazy">
    <figcaption>Aligned <a href="https://space.bilibili.com/1437582453" target="_blank" rel="noopener">Azuma Seren</a></figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/aligned_taffy.png" alt="Aligned Ace Taffy" loading="lazy">
    <figcaption>Aligned <a href="https://space.bilibili.com/1265680561" target="_blank" rel="noopener">Ace Taffy</a></figcaption>
  </figure>
</div>

<div class="p2-grid c2 narrow">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/lian.png" alt="Lian Fourier magnitude" loading="lazy">
    <figcaption><a href="https://space.bilibili.com/1437582453" target="_blank" rel="noopener">Azuma Seren</a> Fourier magnitude</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/taffy.png" alt="Taffy Fourier magnitude" loading="lazy">
    <figcaption><a href="https://space.bilibili.com/1265680561" target="_blank" rel="noopener">Ace Taffy</a> Fourier magnitude</figcaption>
  </figure>
</div>

<div class="p2-grid c3">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/high.png" alt="High-frequency spectrum" loading="lazy">
    <figcaption>High-frequency spectrum</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/low.png" alt="Low-frequency spectrum" loading="lazy">
    <figcaption>Low-frequency spectrum</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/hybrid.png" alt="Hybrid spectrum" loading="lazy">
    <figcaption>Hybrid spectrum</figcaption>
  </figure>
</div>

The low-frequency Fourier magnitude should show its strongest energy near the center, while the high-frequency spectrum should have the visible energy farther from the center. The hybrid spectrum contains contributions from both, which is the frequency-domain counterpart of adding the high-frequency information from one image to the low-frequency information from the other.

#### Frequency-domain analysis

We notice in the low frequency image, most of the information is gathered in the middle cross while in high frequency image, most of the image is not on the axes, but on the quarters

More precisely, after applying `fftshift`, low-frequency energy is concentrated near the center of the Fourier image because the center represents low spatial frequencies. High-frequency energy appears farther from the center. It does not have to be literally on the axes or in the quarters, but the important visual distinction is that the high-frequency component is farther from the center than the low-frequency component.

For the frequency-domain visualization, the magnitude is displayed on a logarithmic scale. A typical computation is

$$
F(u,v)=\log\left(\left|\operatorname{fftshift}(\operatorname{fft2}(I))\right|\right),
$$

which makes both very strong low-frequency values and weaker high-frequency values easier to see at the same time.

### Part 2.3: Gaussian and Laplacian Stacks

A Gaussian stack and Laplacian stack keep the same image dimensions at every level. This is the same implementation as Project 1 except there is no downsampling.

I use `gaussian_stack(img, sigma, levels=4)` and `laplacian_stack(g)`, where level is the number of layers and sigma is the standard deviation of the Gaussian distribution of the filter. The output order of `gaussian_stack` is would be original, blurred once, blurred twice which is the same as saying fine detail, …, coarsest/general shape. The output of `laplacian_stack` is bandpass filter 1 (greatest detail), bandpass filter 2 (less, but still great detail), … the same last layer of the Gaussian stack.

For the Gaussian stack, let $$G_0=I$$ and define successive levels as

$$
G_{i+1}=G_\sigma * G_i.
$$

The images stay at the original height and width; only their spatial frequency content changes. As the level increases, more high-frequency information is removed and the image becomes smoother.

For the Laplacian stack, each level stores the difference between neighboring Gaussian levels:

$$
L_i=G_i-G_{i+1},
$$

with the coarsest Gaussian level kept as the final residual:

$$
L_{N-1}=G_{N-1}.
$$

Thus, the Laplacian stack divides the image into different frequency bands. The first layer contains the finest details, later layers contain progressively coarser structures, and the final residual stores the lowest-frequency content.

<div class="p2-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_3/graphs/laplacian_gaussian.png" alt="Gaussian/Laplacian stack visualization" loading="lazy">
    <figcaption>Gaussian/Laplacian Stack Visualization</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_3/graphs/oraple.png" alt="Oraple stack result / Figure 3.42-style visualization" loading="lazy">
    <figcaption>Oraple Atack Result / Figure 3.42-style Visualization</figcaption>
  </figure>
</div>

The useful property of the Laplacian stack is that it gives me a way to manipulate different frequency bands separately. Instead of cutting an image at one location and accepting a hard boundary, I can later blend corresponding frequency bands with a smoothly varying mask.

The implementation is deliberately a stack rather than a pyramid: no level is downsampled. This means every level can be directly combined with a same-size mask and with the corresponding level from another image.

### Part 2.4: Multiresolution Blending (a.k.a. the oraple!)

The goal here is to use the Gaussian and Laplacian stacks to create a smooth transition between two images rather than making a visibly abrupt cut.

For a binary vertical or horizontal seam, the mask $M$ starts as a step function. For example, with a vertical seam,

$$
M(x,y)=
\begin{cases}
1,&x<x_0\\
0,&x\ge x_0.
\end{cases}
$$

I also create a Gaussian stack of the mask. Let $$M_i$$ be the Gaussian-blurred mask at level $i$. Then for the corresponding Laplacian bands $$L^A_i$$ and $$L^B_i$$, I blend them using

$$
L_i^{blend}=M_i\,L^A_i+(1-M_i)\,L^B_i.
$$

Finally, the blended image is reconstructed by summing the blended Laplacian levels. This works because the mask changes slowly at coarse frequency bands. A hard step in the original mask becomes a smooth transition after Gaussian filtering, so the seam is not equally sharp at every frequency.

**Fixing Seam:** I used two ways to fix seam:

1. Laplacian pyramid `sigma*2**level` for each level. This captures a broader range and makes the intermediate coarser levels capture more detail and make the bandpass spam a larger spam of the spectrum instead of making all low frequency structure fit in the residual image
2. Making the Gaussian mask filter instead of a binary filter for the finest detail layer solves the seam issue significantly

The first idea makes the effective blur scale grow with the level, so the coarser bands represent broader spatial structures. The second is especially important for eliminating the obvious hard boundary: instead of multiplying the finest-level details by a binary left/right switch, a smoothly varying Gaussian mask gradually transfers detail from one image to the other.

#### Oraple

<div class="p2-grid c3">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_3/apple.jpeg" alt="Apple" loading="lazy">
    <figcaption>Apple</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_3/orange.jpeg" alt="Orange" loading="lazy">
    <figcaption>Orange</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_3/oraple.jpeg" alt="Apple + orange multiresolution blend" loading="lazy">
    <figcaption>Apple + Orange Blend</figcaption>
  </figure>
</div>

The Oraple is the classic demonstration because the straight seam can be made visually smooth even though the left and right halves originally come from different images. The low-frequency bands transition gradually, while the Laplacian bands preserve local texture and edge information around the seam.

#### Custom blend 1: Half-peeled shrimp

<div class="p2-grid c3">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_4/peel.jpg" alt="Peeled shrimp" loading="lazy">
    <figcaption>Peeled Crawfish</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_4/unpeel.jpg" alt="Unpeeled shrimp" loading="lazy">
    <figcaption>Unpeeled Shrimp</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_4/half_peeled_shrimp.jpeg" alt="Half-peeled shrimp blend" loading="lazy">
    <figcaption>Shrimp + Crawfish Blend</figcaption>
  </figure>
</div>

<div class="p2-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_4/half_peeled_shrimp.png" alt="Half-peeled shrimp blend, stacks" loading="lazy">
    <figcaption>Shrimp + Crawfish Stack</figcaption>
  </figure>
</div>

This example shows how the same multiresolution blending idea can be used outside the textbook apple/orange example. The mask determines which parts of each input contribute to the final result, while the Gaussian stack of the mask softens the transition. A self-defined align_with_mask() function is made to match the object to the mask with the background image. 

#### Custom blend 2: Hamster + Daifuku

<div class="p2-grid c3">
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_4/dafu.jpg" alt="Daifuku" loading="lazy">
    <figcaption>Daifuku</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/image/Part_2_4/hamster.jpg" alt="Hamster" loading="lazy">
    <figcaption>Hamster</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_4/hamster_dafu.jpeg" alt="Hamster + Dafu blend" loading="lazy">
    <figcaption>Hamster + Daifuku Blend</figcaption>
  </figure>
</div>

<div class="p2-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj2/output/Part_2_4/hamster_dafu.png" alt="Hamster + Dafu blend, stacks" loading="lazy">
    <figcaption>Hamster + Daifuku Stack</figcaption>
  </figure>
</div>

The custom examples demonstrate the more creative side of multiresolution blending. A useful mask does not have to be a straight vertical line. With an irregular mask, different parts of the two images can interleave, and the multiscale process helps the boundaries look more natural than a hard pixel-level cut.

<div class="p2-note" markdown="1">

**Keynote:** `cv` reads in BGR order while `plt` plot in RGB order. You have to reverse the color channels otherwise you would get color flipped image!

</div>

This matters whenever I move an image between OpenCV and Matplotlib. OpenCV conventionally loads color images as BGR, while Matplotlib expects RGB for display. If I forget to reverse the channels, the output can have visibly incorrect colors even though the underlying filtering operation is correct.

#### Results and Creative Exploration

The custom results also helped show why the Gaussian mask is useful. With a binary mask, the transition occurs at one abrupt boundary. With a Gaussian stack, the transition width changes with scale: fine details transition relatively locally, while coarse structures transition over a wider spatial region. This makes the seam much less noticeable.

The output images also show that multiresolution blending is more than simply averaging two pictures. Each spatial frequency band can be controlled independently, which gives much more natural-looking combinations when the two source images have compatible structure.

## Things I learned

What amazed me the most about is the concept about breaking an image into a frequency domain. I always think of only 1d signals being able to break into frequency domain. Seeings how human eyes is able to depict high frequency (fine details) and low frequency (general shape) differently really fascinates me.

The most important connection for me was seeing the same idea appear repeatedly across the entire project. Derivatives emphasize changes, Gaussian filters remove high frequencies, Laplacian differences isolate frequency bands, and multiresolution blending combines those bands in a controlled way. 
<div class="p2-grid c1" style="max-width: 50%">
  <figure>
    <img src="/images/cs180/proj2/cute.jpg" alt="hamster with daifuku" loading="lazy">
    <figcaption>Hamster + daifuku image blending inspiration</figcaption>
  </figure>
</div>

## Credits

Sources for the VTuber hybrid images:

- [Ace Taffy](https://space.bilibili.com/1265680561)
- [Azuma Seren](https://space.bilibili.com/1437582453)

Sources for the custom blend images:

- [自制盐豆大福](https://xhslink.cn/o/8MZ1LTafNCk)
- [把小老鼠擀成饺子皮需要几步](https://xhslink.cn/o/6EXZZQvzBg4)
- <https://x.com/watabieni/status/1759877676658233570>
- <https://xhslink.cn/m/4HHanyJsyJ>