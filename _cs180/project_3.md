---
title: "Project 3: Exploring the Power of Flow Models!"
collection: cs180
permalink: /cs180/project_3/
date: 2026-10-20
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

Part A uses pretrained PixNerd for denoising, sampling, CFG, image editing, visual anagrams, and hybrids. Part B trains MNIST denoisers and time- and class-conditioned flow models from scratch.

I use $t=0$ for pure noise and $t=1$ for a clean image; the network predicts velocity, not noise.

## Part A: Exploring the Power of Flow Models

Part A uses PixNerd-XXL-P16-T2I with Qwen3, float32, seed $180$, and step size $h=0.02$. Guidance uses $w=7$ and an empty unconditional prompt; no training or gradients are needed.

<div class="p3-note" markdown="1">

**Flow matching recap (Part A conventions)**

Interpolate between Gaussian noise $\epsilon\sim\mathcal{N}(0,I)$ and a clean image $x_1$:

$$
x_t=(1-t)\,\epsilon+t\,x_1,\qquad t\in[0,1].
$$

The model learns $v_\theta(x_t,t,c)$ to predict path velocity $x_1-\epsilon$ for condition $c$:

$$
\mathcal{L}(\theta)=\mathbb{E}\left[\left\|v_\theta(x_t,t,c)-(x_1-\epsilon)\right\|_2^2\right].
$$

Sampling follows the learned ODE from $x_0=\epsilon$ to $t=1$:

$$
\frac{dx_t}{dt}=v_\theta(x_t,t,c).
$$

</div>

### Section 1: Visualizing Training and Sampling

#### Choosing my image

For Sections 1.1–1.5 I use my photo of Changbai Mountain's Heaven Lake, rotated by $90^\circ$ and resized to $512\times512$ with RGB in $[-1,1]$.

#### 1.1 Constructing Noisy Training Inputs

Given clean image $x_1$ and noise $\epsilon$, the input at time $t$ is:

$$
x_t=(1-t)\,\epsilon+t\,x_1 .\tag{A.1}
$$

The image is scaled by $t$, noise by $1-t$, and $x_t$ interpolates between them. At $t=0$ the input is pure noise; at $t=1$ it is clean. I verified both endpoints numerically: $x_{0}=\epsilon$ and $x_{1}=x_1$.

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>$t$</th><th>image amplitude $t$</th><th>noise amplitude $1-t$</th></tr></thead>
  <tbody>
    <tr><td>0.25</td><td>0.25</td><td>0.75</td></tr>
    <tr><td>0.50</td><td>0.50</td><td>0.50</td></tr>
    <tr><td>0.75</td><td>0.75</td><td>0.25</td></tr>
  </tbody>
</table>

I reused one noise tensor $\epsilon$ (`noise(SEED)`, seed $180$) at all three times, so only $t$ changes. I saved the original image, $\epsilon$, and the tensors for Sections 1.2–1.5, including the pair needed for the ground-truth velocity.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_1.png" alt="Original image and the noisy versions at t=0.25, t=0.50, and t=0.75" loading="lazy">
    <figcaption>Original image (Changbai Mountain in China)and the noisy versions at $t=0.25$, $t=0.50$, and $t=0.75$</figcaption>
  </figure>
</div>

Larger $t$ keeps more detail: $t=0.75$ is clearest, $t=0.50$ retains large structures, and noise dominates at $t=0.25$.

#### 1.2 Classical Denoising

For a non-learned baseline, I restore the image amplitude in $x_t$ because it is scaled by $t$, then apply Gaussian blur. Averaging nearby pixels reduces noise but also removes detail:

$$
\frac{x_t}{t}=x_1+\frac{1-t}{t}\,\epsilon,\qquad \hat{x}_1^{\mathrm{blur}}=G_\sigma\!\left(\frac{x_t}{t}\right).
$$

Dividing by $t$ restores image amplitude but amplifies noise to standard deviation $\frac{1-t}{t}$: $3$, $1$, and $\tfrac13$ at $t=0.25,\,0.50,\,0.75$. The problem is worse at small $t$.

I tested $\sigma\in\{1,2,4,8,16\}$ (kernel size $6\sigma+1$) and kept the $\sigma$ with lowest MSE against the clean image. This oracle baseline uses ground truth to select $\sigma$; the chosen $\sigma$ appears in each panel title.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_2.png" alt="Noisy input (left) and best Gaussian-denoised result (right) for t=0.25, t=0.50, t=0.75" loading="lazy">
    <figcaption>Noisy input (left) and best Gaussian-denoised result (right) for $t=0.25$, $t=0.50$, $t=0.75$</figcaption>
  </figure>
</div>

Blur reduces noise but also removes detail. At $t=0.25$, the best result needs a large $\sigma$ and keeps only smooth colors; at $t=0.75$, rocks and lake ice remain soft. Gaussian blur cannot separate noise from fine texture.

#### 1.3 One-Step Denoising

The pretrained model returns $v_\theta(x_t,t,c)$ through `predict_velocity(x, t, condition)`. If the true velocity were exactly $x_1-\epsilon$ and constant, one step to $t=1$ would recover the image:

$$
x_t+(1-t)(x_1-\epsilon)=(1-t)\epsilon+t x_1+(1-t)x_1-(1-t)\epsilon=x_1 .
$$

The one-step clean-image estimate is:

$$
\hat{x}_1=x_t+(1-t)\,v_\theta(x_t,t,c).\tag{A.2}
$$

Velocity is added with $(1-t)$, not subtracted. I use the prompt `"a high quality photo"` and verify the prediction's shape, dtype, device, and finite values.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_3.png" alt="Original, noisy input, and one-step estimate for t=0.25, t=0.50, t=0.75" loading="lazy">
    <figcaption>Original, noisy input, and one-step estimate for $t=0.25$, $t=0.50$, $t=0.75$</figcaption>
  </figure>
</div>

The one-step result from $x_t$ beats blur but is still soft. The predicted velocity at $(x_t,t)$ approximates $\mathbb{E}[x_1-\epsilon\mid x_t]$, so $x_t+(1-t)v_\theta$ approximates $\mathbb{E}[x_1\mid x_t]$, an average of plausible clean images. It is blurrier at $t=0.25$ and closer to the original at $t=0.75$.

#### 1.4 Visualizing Velocity

Section 1.3 adds $(1-t)\hat v_t$ to $x_t$. Here I compare it with the exact update $x_1-x_t$.

$$
v_{\mathrm{gt}}=x_1-\epsilon,\qquad (1-t)\,v_{\mathrm{gt}}=x_1-x_t,\qquad \hat v_t=v_\theta(x_t,t,c).
$$

The heatmap shows per-pixel RMS error across RGB, computed before display scaling or clipping:

$$
E_t(i,j)=\sqrt{\operatorname{mean}_{\mathrm{RGB}}\!\left(\Big[(1-t)\big(\hat v_t-v_{\mathrm{gt}}\big)\Big]^2\right)} .
$$

Both updates share a symmetric RGB scale (zero is mid-gray); all error maps use the same limits.

The original $x_1$ is shown once in Section 1.1.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_4_1.png" alt="t=0.25: noisy image x_t, predicted update (1-t)\hat v_t, ground-truth update (1-t)v_{\mathrm{gt}}, and RMS error heatmap" loading="lazy">
    <figcaption>$t=0.25$: noisy image $x_t$, predicted update $(1-t)\hat v_t$, ground-truth update $(1-t)v_{\mathrm{gt}}$, and RMS error heatmap</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_4_2.png" alt="t=0.50: noisy image x_t, predicted update (1-t)\hat v_t, ground-truth update (1-t)v_{\mathrm{gt}}, and RMS error heatmap" loading="lazy">
    <figcaption>$t=0.50$: noisy image $x_t$, predicted update $(1-t)\hat v_t$, ground-truth update $(1-t)v_{\mathrm{gt}}$, and RMS error heatmap</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_4_3.png" alt="t=0.75: noisy image x_t, predicted update (1-t)\hat v_t, ground-truth update (1-t)v_{\mathrm{gt}}, and RMS error heatmap" loading="lazy">
    <figcaption>$t=0.75$: noisy image $x_t$, predicted update $(1-t)\hat v_t$, ground-truth update $(1-t)v_{\mathrm{gt}}$, and RMS error heatmap</figcaption>
  </figure>
</div>

As $t$ grows, both updates shrink with $(1-t)$; at $t=0.75$ only one-quarter is added. The prediction captures large structures but cannot recover the exact noise $\epsilon$, so errors cluster around fine texture and edges.

#### 1.5 Iterative Denoising with Euler Sampling

The velocity changes along the path, so one large step is inaccurate. I recompute it after each small Euler step:

$$
x_{t+h}=x_t+h\,v_\theta(x_t,t,c).\tag{A.3}
$$

Starting at $t_0$ with $h=0.02$, I use $t_k=t_0+kh$ and $N=\lceil (1-t_0)/h\rceil$. The last step is shortened to $h_k=\min(h,\,1-t_k)$ so sampling ends at $t=1$. For $t_0=0.5$ this is $25$ steps; for $t_0=0$ it is $50$.

For $v\equiv 1$, integrating from $t_0$ to $1$ gives $x=x_{\text{start}}+(1-t_0)$. The test matches for $t_0=0$ (50 steps), $t_0=0.5$ (25), and $t_0=0.93$ (4, with a final step of $0.01$).

I run the $25$ Euler updates from the saved $t=0.50$ input with `"a high quality photo"`. The figure shows the start, every fifth update, and the final image (labels: $t=0.50,\,0.60,\,0.70,\,0.80,\,0.90,\,1.00$).

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_5.png" alt="Euler denoising trajectory from t=0.50 to t=1.00 (initial state, every fifth update, final image)" loading="lazy">
    <figcaption>Euler denoising trajectory from $t=0.50$ to $t=1.00$ (initial state, every fifth update, final image)</figcaption>
  </figure>
</div>

I compare the Euler result with the original, blur, and one-step estimate from the same $t=0.50$ input:

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_5_comp.png" alt="Original, Gaussian blur, one-step estimate, and Euler denoising from the same t=0.50 input" loading="lazy">
    <figcaption>Original, Gaussian blur, one-step estimate, and Euler denoising from the same $t=0.50$ input</figcaption>
  </figure>
</div>

Euler sampling recovers large structures first, then details. For the same $t=0.50$ input, it gives the sharpest result because each step re-evaluates velocity on a less-noisy image; blur and one-step denoising remain soft.

#### 1.6 Generating Images from Noise

I generate five images from pure noise with $t_{\text{start}}=0$, $50$ Euler updates, and `"a high quality photo"` (seeds $180$–$184$). I reuse these noises in Section 2.1.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_6.png" alt="Five samples with the prompt 'a high quality photo' (no guidance), seeds 180–184" loading="lazy">
    <figcaption>Five samples with the prompt "a high quality photo" (no guidance), seeds 180–184</figcaption>
  </figure>
</div>

The generic prompt gives plausible but soft images with limited variety. CFG should improve sharpness and prompt alignment.

### Section 2: Conditional Generation and Guidance

#### 2.1 Classifier-Free Guidance (CFG)

For CFG, I evaluate the same $x_t$ and $t$ with the text prompt ($v_c$) and empty prompt ($v_u$), then extrapolate past the conditional prediction:

$$
v_{\mathrm{cfg}}=v_c+w\,(v_c-v_u).\tag{A.4}
$$

I use $w=7$ and $h=0.02$; $w=0$ gives ordinary conditional sampling. Equivalently, $v_{\mathrm{cfg}}=v_u+(1+w)(v_c-v_u)$, so guidance scales the direction $v_c-v_u$ by $1+w$.

I compare five guided samples at seeds $180$–$184$ with the unguided outputs from Section 1.6.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_6.png" alt="Section 1.6: no guidance (w=0), seeds 180–184" loading="lazy">
    <figcaption>Section 1.6: no guidance ($w=0$), seeds 180–184</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_2/2_1.png" alt="Section 2.1: classifier-free guidance, w=7, same seeds 180–184" loading="lazy">
    <figcaption>Section 2.1: classifier-free guidance, $w=7$, same seeds 180–184</figcaption>
  </figure>
</div>

With the same noise, CFG produces sharper, more saturated, more prompt-aligned images. Samples are less diverse because $1+w=8$ pushes them strongly along $v_c-v_u$; large $w$ can oversaturate colors.

### Section 3: Creative Applications

I use flow sampling for image editing and optical illusions, with Euler step size $h=0.02$, CFG weight $w=7$, and `""` as the unconditional prompt.

#### 3.1 Image-to-Image Editing

Following SDEdit, I noise a real image $x_1$ to starting time $t_0$, then run guided sampling from $t_0$ to $1$:

$$
x_{t_0}=(1-t_0)\,\epsilon+t_0\,x_1,\qquad x_{t+h}=x_t+h\,v_{\mathrm{cfg}}(x_t,t,c)\quad\text{for } t=t_0,\dots,1 .
$$

Smaller $t_0$ adds more noise and changes more; larger $t_0$ preserves the input. The starting time $t_0$ is the only variable across each row. I reuse seed $180$ for $\epsilon$ and test $t_0\in\{0.02,0.04,0.06,0.10,0.14,0.25,0.50\}$ with `"a high quality photo"`.

For each start time $t_0$, Euler updates are given by $\lceil(1-t_0)/h\rceil$:

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>start time $t_0$</th><th>Euler updates</th></tr></thead>
  <tbody>
    <tr><td>0.02</td><td>49</td></tr>
    <tr><td>0.04</td><td>48</td></tr>
    <tr><td>0.06</td><td>47</td></tr>
    <tr><td>0.10</td><td>45</td></tr>
    <tr><td>0.14</td><td>43</td></tr>
    <tr><td>0.25</td><td>38</td></tr>
    <tr><td>0.50</td><td>25</td></tr>
  </tbody>
</table>

For $t_0\le 0.06$, the input is nearly pure noise; at $t_0=0.50$, the original is much better preserved.

**Campanile.**

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/edit_campanile.png" alt="Campanile, prompt 'a high quality photo', starting times t_0=0.02\dots0.50" loading="lazy">
    <figcaption>Campanile, prompt "a high quality photo", starting times $t_0=0.02\dots0.50$</figcaption>
  </figure>
</div>

As $t_0$ increases, the output moves from an unrelated image toward the original Campanile. Its layout returns in the middle range; at $t_0=0.50$, the edit is close to the input.

My test images are a pancake-and-blueberry plate (`pancake`) and a dessert cup from Yogurt Park (`yopo`), rotated $90^\circ$ clockwise.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/pancake_edit_my_image_1.png" alt="My image 1 (pancake plate), prompt 'a high quality photo'" loading="lazy">
    <figcaption>My image 1 (Pancake Plate), prompt "a high quality photo"</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/yopo_edit_my_image_2.png" alt="My image 2 (cup of dessert), prompt 'a high quality photo'" loading="lazy">
    <figcaption>My image 2 (Cup of Dessert at Yogurt Park), prompt "a high quality photo"</figcaption>
  </figure>
</div>

For `yopo`, $t_0=0.02$ produces a man's face; $t_0=0.04$ and $0.06$ retain only the hand and invent an object. At $t_0=0.10$ and $0.14$, only the hand and some colors remain. The cup returns at $t_0=0.25$, and $t_0=0.50$ is close to the input.

#### Editing hand-drawn and web images

This sweep shows how editing pushes non-realistic inputs toward natural-looking images. I test one web image and two hand drawings.

**Web image: Moon Lord from Terraria.** The input is flat pixel art, so it starts far from the natural-image manifold. At $t_0=0.02$ the original is gone and the model gives an unrelated photo of a man with headphones at a desk. At $t_0=0.04$ and $0.06$ only the pose and the palette survive: the raised arms and open hands become a real man in a tan T-shirt, and the teal head becomes a glossy blue or teal helmet. At $t_0=0.10$ and $0.14$ the model keeps the symmetric layout and the teal glow but renders everything as glass, metal, and skin. The head turns into a translucent capsule with glowing teal orbs where the eyes were, the ribcage turns into metal rings, and the wrists get bracelets. At $t_0=0.25$ the Moon Lord returns with its tentacle face, brain, and palm eyes, but with smooth shading and realistic muscles instead of pixels. By $t_0=0.50$ it is almost the original pixel art, with only slightly softer outlines.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/moon_lord_edit_web_image.png" alt="Web image (Moon Lord), prompt 'a high quality photo'" loading="lazy">
    <figcaption>Web image (Moon Lord), prompt "a high quality photo"</figcaption>
  </figure>
</div>

**Hand drawing 1: Golden Gate Bridge.** At $t_0=0.02$ the model gives a portrait; at $t_0=0.04$ and $0.06$, only colors remain. At $t_0=0.10$ and $0.14$, the tower and cables become a photo-style bridge. For $t_0\ge0.25$, the flat drawing returns.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/golden_gate_edit_drawing_1.png" alt="Hand drawing 1 (Golden Gate), prompt 'a high quality photo'" loading="lazy">
    <figcaption>Hand drawing 1 (Golden Gate), prompt "a high quality photo"</figcaption>
  </figure>
</div>

**Hand drawing 2: Tunnel View at Yosemite National Park.** At $t_0=0.02$ the model gives an unrelated portrait; at $t_0=0.04$ and $0.06$, the blocks become a building. At $t_0=0.10$ and $0.14$, the valley becomes photorealistic with a few drawing marks left. The flat drawing returns at $t_0=0.25$.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/yosemite_edit_drawing_2.png" alt="Hand drawing 2 (Yosemite), prompt 'a high quality photo'" loading="lazy">
    <figcaption>Hand drawing 2 (Yosemite), prompt "a high quality photo"</figcaption>
  </figure>
</div>

The best range for turning drawings into photos is $t_0=0.10$–$0.14$: enough structure remains to guide composition, while noise lets the model add photo textures.

**Paintings from my sister**: a cat painting and a Van Gogh painting.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/cat_edit_drawing_3.png" alt="Extra: cat image, prompt 'a high quality photo'" loading="lazy">
    <figcaption>Extra: cat painting, prompt "a high quality photo"</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/van_gogh_edit_drawing_4.png" alt="Extra: Van Gogh painting, prompt 'a high quality photo'" loading="lazy">
    <figcaption>Extra: Van Gogh painting, prompt "a high quality photo"</figcaption>
  </figure>
</div>

#### 3.2 Text-Guided Image Editing

I use the same procedure as Section 3.1, but describe the desired edit. Smaller $t_0$ favors the prompt; larger $t_0$ preserves the original.

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Image</th><th>Prompt</th></tr></thead>
  <tbody>
    <tr><td>Campanile</td><td><code>the big ben</code></td></tr>
    <tr><td>My image 1 (pancake)</td><td><code>A seafood platter with lobster, crab, shrimp, mussels, and lemon wedges, photorealistic.</code></td></tr>
    <tr><td>My image 2 (dessert cup)</td><td><code>male with pink and green hair</code></td></tr>
  </tbody>
</table>

**Campanile with the prompt `"the big ben"`.**

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/text_edit_campanile.png" alt="Campanile, prompt 'the big ben', starting times t_0=0.02\dots0.50" loading="lazy">
    <figcaption>Campanile, prompt "the big ben", starting times $t_0=0.02\dots0.50$</figcaption>
  </figure>
</div>

At small $t_0$, Big Ben dominates; at large $t_0$, the Campanile returns. In between, its tower silhouette remains but takes on Big Ben's style.

**Pancake plate to a seafood feast.** At $t_0=0.02$ to $0.06$, the pancake is replaced by a seafood platter. At $t_0=0.10$ and $0.14$, the plate composition remains while seafood replaces the food. At $t_0=0.25$, pancake and seafood features mix; at $t_0=0.50$, the original pancake mostly returns.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/pancake_seafood_text_edit_my_image_1.png" alt="My image 1 (pancake), prompt: seafood feast on a large platter" loading="lazy">
    <figcaption>My image 1 (pancake), prompt: seafood feast on a large platter</figcaption>
  </figure>
</div>

**Dessert cup to a man with pink and green hair.** For $t_0\le0.06$, the model generates an unrelated portrait. At $t_0=0.10$ and $0.14$, it uses the image's pink and green as hair while retaining some background colors. At $t_0=0.25$, hair grows from the cup; at $t_0=0.50$, the cup returns with a pastel tint.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/yopo_man_text_edit_my_image_2.png" alt="My image 2 (dessert cup), prompt: 'male with pink and green hair'" loading="lazy">
    <figcaption>My image 2 (dessert cup), prompt: "male with pink and green hair"</figcaption>
  </figure>
</div>

Unlike the generic prompt in Section 3.1, the dessert cup is lost for $t_0\le0.14$ and the model invents faces or objects. The pink/green prompt reuses the image's colors to build the requested hair.

#### 3.3 Visual Anagrams

A visual anagram shows one subject upright and another after a $180^\circ$ rotation. I denoise $x_t$ with prompt $p_1$ and the rotated image with $p_2$, then rotate the second velocity back and average:

$$
v_1=v_{\mathrm{cfg}}(x_t,t,p_1),
$$

$$
v_2=\operatorname{flip}\!\Big(v_{\mathrm{cfg}}\big(\operatorname{flip}(x_t),\,t,\,p_2\big)\Big),
$$

$$
v=\frac{v_1+v_2}{2},\qquad x_{t+h}=x_t+h\,v .
$$

Here $\operatorname{flip}(\cdot)$ rotates by $180^\circ$ across both spatial axes, so $\operatorname{flip}(\operatorname{flip}(x))=x$. At each step, I evaluate $\operatorname{flip}$ on $x_t$ with $p_1$ and on the rotated $x_t$ with $p_2$. Pixel permutation preserves the noise distribution; averaging the guided velocities encourages both views.

Each result is shown upright for $p_1$ and rotated $180^\circ$ for $p_2$. I tried multiple prompt/seed combinations because some runs do not make both subjects recognizable.

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Illusion</th><th>Upright prompt $p_1$</th><th>Rotated prompt $p_2$</th><th>Seed</th></tr></thead>
  <tbody>
    <tr><td>1</td><td>an oil painting of an old man</td><td>an oil painting of people around a campfire</td><td>180</td></tr>
    <tr><td>2</td><td>a lithograph of a snowy mountain village</td><td>a lithograph of a horse</td><td>181</td></tr>
    <tr><td>3</td><td>watercolor painting of angel</td><td>watercolor painting of demon</td><td>180</td></tr>
    <tr><td>4</td><td>watercolor painting of ancient chinese village</td><td>watercolor painting of medieval european-style town</td><td>180</td></tr>
  </tbody>
</table>

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/old_man_campfire_anagram_comparison.png" alt="Illusion 1: old man (upright) and people around a campfire (rotated 180^\circ)" loading="lazy">
    <figcaption>Illusion 1: old man (upright) and people around a campfire (rotated $180^\circ$)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/village_horse_anagram_comparison.png" alt="Illusion 2: snowy mountain village (upright) and a horse (rotated 180^\circ)" loading="lazy">
    <figcaption>Illusion 2: snowy mountain village (upright) and a horse (rotated $180^\circ$)</figcaption>
  </figure>
</div>

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/angel_demon_anagram_comparison.png" alt="Illusion 3: angel (upright) and demon (rotated 180^\circ)" loading="lazy">
    <figcaption>Illusion 3: angel (upright) and demon (rotated $180^\circ$)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/chinese_european_village_anagram_anagram_comparison.png" alt="Illusion 4: ancient Chinese village (upright) and medieval European-style town (rotated 180^\circ)" loading="lazy">
    <figcaption>Illusion 4: ancient Chinese village (upright) and medieval European-style town (rotated $180^\circ$)</figcaption>
  </figure>
</div>

For illusions 3 and 4, here are the generated images themselves, in the orientation of the upright prompt $p_1$:

<div class="p3-grid c2 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/angel_demon_anagram.png" alt="Illusion 3: generated image, upright view (angel)" loading="lazy">
    <figcaption>Illusion 3: generated image, upright view (angel)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/chinese_european_village_anagram.png" alt="Illusion 4: generated image, upright view (ancient Chinese village)" loading="lazy">
    <figcaption>Illusion 4: generated image, upright view (ancient Chinese village)</figcaption>
  </figure>
</div>

The old man/campfire and angel/demon pairs work best because their shapes map well after rotation. The village/horse and Chinese/European village pairs are weaker: details overlap or the scenes look too similar. Similar layouts, tones, and style words help both views remain recognizable.

#### 3.4 Hybrid Images

I combine low-frequency velocity from one prompt with high-frequency velocity from another to create a close/far hybrid:

$$
v_1=v_{\mathrm{cfg}}(x_t,t,p_1),\qquad v_2=v_{\mathrm{cfg}}(x_t,t,p_2),
$$

$$
v=f_{\mathrm{lowpass}}(v_1)+f_{\mathrm{highpass}}(v_2),\qquad f_{\mathrm{highpass}}(v)=v-f_{\mathrm{lowpass}}(v),
$$

$$
x_{t+h}=x_t+h\,v .
$$

$f_{\mathrm{lowpass}}$ is Gaussian blur with kernel size $265$ and $\sigma=16$ at $512\times512$ (formula: $2\cdot\mathrm{round}(16.5\cdot512/64)+1=265$). The coarse structure of $x$ follows $p_1$ (far view), and fine detail follows $p_2$ (close view). Thus $p_1$ is the distant prompt and $p_2$ the close prompt.

Each hybrid is shown at $512\times512$ and reduced to $64\times64$ to reveal its low-frequency view.

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Hybrid</th><th>Distant (low-frequency) prompt $p_1$</th><th>Close (high-frequency) prompt $p_2$</th></tr></thead>
  <tbody>
    <tr><td>1</td><td>a lithography of a cat</td><td>a lithography of a dog</td></tr>
    <tr><td>2</td><td>a lithography of a temple</td><td>a lithography of a church</td></tr>
  </tbody>
</table>

<div class="p3-grid c2 medium" style="align-items:flex-end;">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/cat_dog_hybrid_0.png" alt="Hybrid 1 full size" loading="lazy">
    <figcaption>Hybrid 1, close view (full size): "a lithography of a dog"</figcaption>
  </figure>
  <figure style="flex: 0 0 auto;">
    <img src="/images/cs180/proj3/part_a/part_3/cat_dog_hybrid_0.png" alt="Hybrid 1 reduced to 64 by 64" style="width:64px; min-width:64px; image-rendering:auto;" loading="lazy">
    <figcaption>Hybrid 1, distant view ($64\times64$): "a lithography of a cat"</figcaption>
  </figure>
</div>

<div class="p3-grid c2 medium" style="align-items:flex-end;">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/church_temple_hybrid_0.png" alt="Hybrid 2 full size" loading="lazy">
    <figcaption>Hybrid 2, close view (full size): "a lithography of a church"</figcaption>
  </figure>
  <figure style="flex: 0 0 auto;">
    <img src="/images/cs180/proj3/part_a/part_3/church_temple_hybrid_0.png" alt="Hybrid 2 reduced to 64 by 64" style="width:64px; min-width:64px; image-rendering:auto;" loading="lazy">
    <figcaption>Hybrid 2, distant view ($64\times64$): "a lithography of a temple"</figcaption>
  </figure>
</div>

Hybrid 1 reads as a dog at full size, but at $64\times64$ its cat silhouette is weak. Hybrid 2 reads mostly as a church; the temple is hard to see in the reduced version. High-frequency detail tends to win, so distinct coarse silhouettes with similar tones work best.

## Part B: Flow Matching from Scratch!

In Part B I train MNIST denoisers and flow models from scratch. I compare one-step denoising with iterative generation, using time conditioning, class conditioning, and CFG. Models use the 60,000-image MNIST training set, pixels in $[0,1]$, fresh noise, seed $180$, and a Tesla T4 GPU.

### Part 1: Training a Single-Step Denoising UNet

I train a UNet denoiser $D_\theta$ to map noisy image $z$ to clean image $x$ using L2 loss:

$$
L=\mathbb{E}_{z,x}\left\|D_\theta(z)-x\right\|^2 .\tag{B.1}
$$

#### 1.1 Implementing the UNet

The UNet has two downsampling blocks, a bottleneck, two upsampling blocks, and channel-wise skip connections. Layers use batch normalization (`BN`) and `GELU`:

- **Conv**: `Conv2d(3, 1, 1)` + BN + GELU. Changes only the number of channels.
- **DownConv**: `Conv2d(3, 2, 1)` + BN + GELU. Halves the height and width.
- **UpConv**: `ConvTranspose2d(4, 2, 1)` + BN + GELU. Doubles the height and width.
- **Flatten**: `AvgPool2d(7)` + GELU. Turns a $7\times7$ map into $1\times1$.
- **Unflatten**: `ConvTranspose2d(7, 7, 0)` + BN + GELU. Turns a $1\times1$ map back into $7\times7$.
- **ConvBlock**: Conv → Conv. **DownBlock**: DownConv → ConvBlock. **UpBlock**: UpConv → ConvBlock. These only add depth and do not change the shape.

$D$ is the hidden width. For input $1\times28\times28$, the network shapes are:

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Stage</th><th>Operation</th><th>Output shape</th></tr></thead>
  <tbody>
    <tr><td>input</td><td></td><td>$1\times28\times28$</td></tr>
    <tr><td>x1</td><td>ConvBlock</td><td>$D\times28\times28$</td></tr>
    <tr><td>x2</td><td>DownBlock</td><td>$D\times14\times14$</td></tr>
    <tr><td>x3</td><td>DownBlock</td><td>$2D\times7\times7$</td></tr>
    <tr><td>z</td><td>Flatten, then Unflatten</td><td>$2D\times1\times1$, then $2D\times7\times7$</td></tr>
    <tr><td>u1</td><td>Concat(x3, z) ($4D$ channels), then UpBlock</td><td>$D\times14\times14$</td></tr>
    <tr><td>u2</td><td>Concat(x2, u1) ($2D$ channels), then UpBlock</td><td>$D\times28\times28$</td></tr>
    <tr><td>output</td><td>Concat(x1, u2) ($2D$ channels), ConvBlock, then <code>Conv2d(3,1,1)</code></td><td>$1\times28\times28$</td></tr>
  </tbody>
</table>

#### 1.2 Using the UNet to Train a Denoiser

I create training pairs $(z,x)$, where $x$ is a clean MNIST image. I make $z$ from $x$ using:

$$
z=x+\sigma\epsilon,\qquad \epsilon\sim\mathcal{N}(0,I),\tag{B.2}
$$

With $x\in[0,1]$, the figure uses $\sigma\in\{0.0,0.2,0.4,0.5,0.6,0.8,1.0\}$. Increasing $\sigma$ obscures the digits; at $\sigma=1.0$, noise nearly hides them.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2.png" alt="Noising process z=x+\sigma\epsilon for \sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0 (five digits)" loading="lazy">
    <figcaption>Noising process $z=x+\sigma\epsilon$ for $\sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0$ (five digits)</figcaption>
  </figure>
</div>

##### 1.2.1 Training

I train the denoiser with **$\sigma=0.5$**:

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <tbody>
    <tr><td>Model</td><td>UNet, hidden dimension $D=128$</td></tr>
    <tr><td>Data</td><td>MNIST train set, shuffled, batch size 256 (235 iterations per epoch)</td></tr>
    <tr><td>Noise</td><td>$z=x+0.5\,\epsilon$, a <strong>fresh</strong> $\epsilon$ every time a batch is fetched, so each epoch sees new noisy versions</td></tr>
    <tr><td>Loss / optimizer</td><td>MSE (B.1), Adam, learning rate $10^{-4}$, no scheduler</td></tr>
    <tr><td>Epochs</td><td>5</td></tr>
  </tbody>
</table>

**Training loss curve.** Loss drops sharply in the first ~100 iterations, then more slowly. Last-batch losses for epochs $1$ to $5$ are $0.0120,\ 0.0101,\ 0.0092,\ 0.0091,\ 0.0085$.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_1_loss_graph.png" alt="Training loss (MSE) of the \sigma=0.5 denoiser over all 5 epochs" loading="lazy">
    <figcaption>Training loss (MSE) of the $\sigma=0.5$ denoiser over all 5 epochs</figcaption>
  </figure>
</div>

**Test-set results** use the same digits and noise for comparison. Rows show clean, noisy ($\sigma=0.5$), and denoised images.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_1_epoch_1.png" alt="Test-set results after epoch 1 (rows: clean / noisy \sigma=0.5 / denoised)" loading="lazy">
    <figcaption>Test-set results after epoch 1 (rows: clean / noisy $\sigma=0.5$ / denoised)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_1_epoch_5.png" alt="Test-set results after epoch 5 (rows: clean / noisy \sigma=0.5 / denoised)" loading="lazy">
    <figcaption>Test-set results after epoch 5 (rows: clean / noisy $\sigma=0.5$ / denoised)</figcaption>
  </figure>
</div>

After epoch 1, digits are recognizable but grainy; by epoch 5, strokes are smoother and backgrounds cleaner.

##### 1.2.2 Out-of-Distribution Testing

The model was trained only at $\sigma=0.5$. I keep the same test digits and noise direction $\epsilon$ and vary $\sigma\in\{0.0,0.2,0.4,0.5,0.6,0.8,1.0\}$, so $z=x+\sigma\epsilon$ for fixed $\epsilon$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_2.png" alt="Denoiser trained at \sigma=0.5, tested at \sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0 (noisy rows and denoised rows)" loading="lazy">
    <figcaption>Denoiser trained at $\sigma=0.5$, tested at $\sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0$ (noisy rows and denoised rows)</figcaption>
  </figure>
</div>

For $\sigma\le0.5$, the model restores digits well, even at $\sigma=0.2$ and $0.4$. Clean inputs ($\sigma=0.0$) become slightly blurry. Larger noise ($\sigma=0.6$ and above) degrades results: strokes break at $\sigma=0.6$, while $\sigma=0.8$ and $1.0$ produce artifacts.

##### 1.2.3 Denoising Pure Noise

I repeat training with pure noise $z=\epsilon\sim\mathcal{N}(0,I)$ as input and clean digits as targets, using the same $D=128$ UNet, Adam $10^{-4}$, batch 256, and 5 epochs as Section 1.2.1.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_3_loss_graph.png" alt="Training loss of the pure-noise denoiser over all 5 epochs" loading="lazy">
    <figcaption>Training loss of the pure-noise denoiser over all 5 epochs</figcaption>
  </figure>
</div>

Loss plateaus near $0.067$, above the $\sigma=0.5$ denoiser's $0.0085$. Last-batch losses are $0.0679,\ 0.0685,\ 0.0654,\ 0.0741,\ 0.0679$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_3_epoch_1.png" alt="Pure-noise denoiser after epoch 1 (rows: clean digits / noise input / output)" loading="lazy">
    <figcaption>Pure-noise denoiser after epoch 1 (rows: clean digits / noise input / output)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_3_epoch_5.png" alt="Pure-noise denoiser after epoch 5 (rows: clean digits / noise input / output)" loading="lazy">
    <figcaption>Pure-noise denoiser after epoch 5 (rows: clean digits / noise input / output)</figcaption>
  </figure>
</div>

**What patterns do I observe?** Every noise input produces the same blurry, average-looking blob rather than a digit. Epoch 5 is smoother than epoch 1 but has the same shape.

**Why?** The pure-noise input $z$ is independent of digit $x$, so it gives no class information. Under MSE, the best fixed prediction minimizes squared distance to the training images. For any fixed input $z$, it is:

$$
D^*(z)=\arg\min_f\ \mathbb{E}_x\|f-x\|^2=\mathbb{E}[x\mid z]=\mathbb{E}[x]=\bar{x}.
$$

The output is the training-set mean (centroid) of digits $0$–$9$, bright where strokes overlap and blurry where they cancel. The loss plateaus at $\mathbb{E}\|x-\bar{x}\|^2$ per pixel. With no digit information, one-step denoising returns an average; flow matching adds information over many steps.

### Part 2: Training a Flow Matching Model

One-step denoising fails at generation, so I use flow matching: sample noise $x_0\sim\mathcal{N}(0,I)$ and interpolate toward a clean training image $x_1$. For clean image $x_1$, the sample at time $t$ is:

$$
x_t=(1-t)\,x_0+t\,x_1,\qquad x_0\sim\mathcal{N}(0,I),\quad t\in[0,1].\tag{B.3}
$$

Small $t$ is close to noise; large $t$ approaches the data. The path's **flow** (velocity) is:

$$
u(x_t,t)=\frac{d}{dt}x_t=x_1-x_0.\tag{B.4}
$$

The UNet $u_\theta(x_t,t)$ learns this velocity by minimizing:

$$
L=\mathbb{E}_{x_0\sim p_0,\ x_1\sim p_1,\ t\sim U[0,1]}\left\|(x_1-x_0)-u_\theta(x_t,t)\right\|^2.\tag{B.5}
$$

The network predicts flow $x_1-x_0$, not the clean image. Since many $(x_0,x_1)$ pairs can produce the same $(x_t,t)$, it learns the conditional mean $\mathbb{E}[x_1-x_0\mid x_t,t]$; integrating this field generates samples.

#### 2.1 Adding Time Conditioning to UNet

The model needs to know $t$ because the same $x_t$ requires a different velocity at different times. I inject the scalar $t$ with FCBlock: `Linear(F_in, F_out)` → `GELU` → `Linear(F_out, F_out)`. Since $t$ is scalar, $F_{in}=1$; I normalize $t$ to $[0,1]$ before embedding.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/fc_long.png" alt="FCBlock diagram" loading="lazy">
    <figcaption>FCBlock: a Linear layer from F_in to F_out followed by GELU, then a Linear layer from F_out to F_out</figcaption>
  </figure>
</div>

<div class="p3-grid c1">
  <figure>
    <img src="/images/cs180/proj3/part_b/conditional_arch_fm.png" alt="Time-conditioned UNet architecture" loading="lazy">
    <figcaption>Time-conditioned UNet: two FCBlocks inject the time t (red box) into the unflatten output and into the first up-block</figcaption>
  </figure>
</div>

I modulate decoder features by channel-wise multiplication. Here $t_1=\mathrm{FCBlock}_1(t)\in\mathbb{R}^{2D}$ and $t_2=\mathrm{FCBlock}_2(t)\in\mathbb{R}^{D}$, reshaped to $2D\times1\times1$ and $D\times1\times1$ so each channel's scalar is broadcast over all spatial positions. Writing $k$ for the channel index and $(h,w)$ for the spatial position:

$$
\text{unflatten}'_{k,h,w}=t_{1,k}\,\text{unflatten}_{k,h,w},\qquad \text{up1}'_{k,h,w}=t_{2,k}\,\text{up1}_{k,h,w},
$$

or in short, $\text{unflatten}'=\text{unflatten}\odot t_1$ and $\text{up1}'=\text{up1}\odot t_2$, where $\odot$ is the element-wise (Hadamard) product after broadcasting. The primes only mean "the modulated version of" the original tensor.

#### 2.2 Training the UNet

Algorithm B.1 trains time-conditioned UNet $u_\theta(x_t,t)$ to predict flow $x_1-x_0$. It samples clean image $x_1$, time $t\sim U[0,1]$, and noise $x_0\sim\mathcal{N}(0,I)$, builds $x_t$ using (B.3), and minimizes flow error. Each batch sample gets its own $t$ and $x_0$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/algo1_t_only_fm.png" alt="Algorithm B.1: training the time-conditioned UNet" loading="lazy">
    <figcaption>Algorithm B.1: training the time-conditioned UNet</figcaption>
  </figure>
</div>

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Setting</th><th>Value</th></tr></thead>
  <tbody>
    <tr><td>Model</td><td>TimeConditionalUNet, hidden dimension $D=64$</td></tr>
    <tr><td>Data</td><td>MNIST train set, shuffled, batch size 64 (938 iterations per epoch)</td></tr>
    <tr><td>Optimizer</td><td>Adam, initial learning rate $10^{-2}$</td></tr>
    <tr><td>Scheduler</td><td><code>ExponentialLR</code>, $\gamma=0.1^{1/\text{num\_epochs}}$, <code>scheduler.step()</code> once per epoch (the learning rate goes from $10^{-2}$ down to $10^{-3}$)</td></tr>
    <tr><td>Epochs</td><td>10</td></tr>
  </tbody>
</table>

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_2.png" alt="Training loss of the time-conditioned UNet over all 10 epochs (9,380 iterations)" loading="lazy">
    <figcaption>Training loss of the time-conditioned UNet over all 10 epochs (9,380 iterations)</figcaption>
  </figure>
</div>

At learning rate $10^{-2}$, loss falls from above $2$ to about $0.09$–$0.10$. Last-batch losses are $0.1445$, $0.0944$, and $0.0961$ after epochs 1, 5, and 10. It stays nonzero because the target flow $x_1-x_0$ is ambiguous for a fixed $x_t$, so the model learns an average flow for $x_t$.

#### 2.3 Sampling from the UNet

Algorithm B.2 starts from noise $x_0$ and takes $T=50$ Euler steps of size $1/T$ from $t=0$ to $t=1$, evaluating the network at each $x_t$ and $t$:

$$
x_{t+1/T}=x_t+\frac{1}{T}\,u_\theta(x_t,t),\qquad t=0,\tfrac{1}{T},\dots,\tfrac{T-1}{T}.
$$

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/algo2_t_only_fm.png" alt="Algorithm B.2: sampling from the time-conditioned UNet" loading="lazy">
    <figcaption>Algorithm B.2: sampling from the time-conditioned UNet</figcaption>
  </figure>
</div>

I sample $40$ digits from checkpoints at epochs $1$, $5$, and $10$, reusing the same $40$ starting noises for comparison.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_3_epoch_1.png" alt="Time-conditioned flow matching, samples after epoch 1" loading="lazy">
    <figcaption>Time-conditioned flow matching: 40 samples after epoch 1</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_3_epoch_5.png" alt="Time-conditioned flow matching, samples after epoch 5" loading="lazy">
    <figcaption>Time-conditioned flow matching: 40 samples after epoch 5</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_3_epoch_10.png" alt="Time-conditioned flow matching, samples after epoch 10" loading="lazy">
    <figcaption>Time-conditioned flow matching: 40 samples after epoch 10</figcaption>
  </figure>
</div>

Epoch 1 outputs are mostly fragmented strokes; epoch 5 is more recognizable but still distorted. Epoch 10 improves quality, but some digits remain malformed. Time-only conditioning cannot specify which digit to draw, so samples can mix classes.

#### 2.4 Adding Class-Conditioning to UNet

I add the digit class $c$, with $c\in\{0,\dots,9\}$, as a one-hot vector and use two FCBlocks: $F_{in}=10$, $c_1=\mathrm{FCBlock}_1(c)\in\mathbb{R}^{2D}$, and $c_2=\mathrm{FCBlock}_2(c)\in\mathbb{R}^{D}$. Class and time embeddings both modulate the features: the class embedding scales each channel and the time embedding shifts it (both broadcast over $h,w$):

$$
\text{unflatten}'_{k,h,w}=c_{1,k}\,\text{unflatten}_{k,h,w}+t_{1,k},\qquad \text{up1}'_{k,h,w}=c_{2,k}\,\text{up1}_{k,h,w}+t_{2,k},
$$

i.e. $\text{unflatten}'=c_1\odot\text{unflatten}+t_1$ and $\text{up1}'=c_2\odot\text{up1}+t_2$.

For CFG, I drop class $c$ to the zero vector with probability $p_{\text{uncond}}=0.1$, independently for each sample.

#### 2.5 Training the UNet

Algorithm B.3 uses one-hot class $c$ and replaces it with zero with probability $p_{\text{uncond}}=0.1$. The network learns conditional $u_\theta(x_t,t,c)$ and unconditional $u_\theta(x_t,t,0)$ flows.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/algo3_c_fm.png" alt="Algorithm B.3: training the class-conditioned UNet" loading="lazy">
    <figcaption>Algorithm B.3: training the class-conditioned UNet</figcaption>
  </figure>
</div>

I use $D=64$, batch size $64$, Adam at $10^{-2}$, `ExponentialLR` with $\gamma=0.1^{1/10}$, $10$ epochs, and $p_{\text{uncond}}=0.1$. Checkpoints are saved at epochs $1$, $5$, and $10$.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_5_loss_scheduler.png" alt="Training loss of the class-conditioned UNet (with the exponential learning-rate scheduler)" loading="lazy">
    <figcaption>Training loss of the class-conditioned UNet (with the exponential learning-rate scheduler)</figcaption>
  </figure>
</div>

Loss drops from an initial spike near $12$ at learning rate $10^{-2}$ to about $0.1$. Last-batch losses are $0.1227$, $0.0822$, and $0.1030$ at epochs 1, 5, and 10. These single-batch values fluctuate; sample quality improves even when loss looks flat.

#### 2.6 Sampling from the UNet

At each of $T=300$ Euler steps, I evaluate unconditional and class-$c$ flows at the same $(x_t,t)$ and apply CFG with $\gamma=5.0$:

$$
u=u_{\mathrm{uncond}}+\gamma\,(u_{\mathrm{cond}}-u_{\mathrm{uncond}}),\qquad x_{t+1/T}=x_t+\frac{1}{T}\,u .
$$

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/algo4_c_fm.png" alt="Algorithm B.4: class-conditional sampling with classifier-free guidance" loading="lazy">
    <figcaption>Algorithm B.4: class-conditional sampling with classifier-free guidance</figcaption>
  </figure>
</div>

<div class="p3-note" markdown="1">

**Note: two CFG conventions.** Part A uses $v_c+w(v_c-v_u)$; Part B uses $u_u+\gamma(u_c-u_u)$. They match when $\gamma=1+w$ because $v_c+w(v_c-v_u)=v_u+(1+w)(v_c-v_u)$. Thus $\gamma=5$ equals $w=4$, and $\gamma=1$ is conditional sampling.

</div>

I generate 4 samples per digit ($4\times10$ grid, with each row $0,1,\dots,9$) using $300$ Euler steps, $\gamma=5$, and the same seed at epochs $1$, $5$, and $10$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_epoch_1.png" alt="Class-conditioned flow matching with CFG (\gamma=5), samples after epoch 1" loading="lazy">
    <figcaption>Class-conditioned flow matching with CFG ($\gamma=5$), samples after epoch 1</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_epoch_5.png" alt="Class-conditioned flow matching with CFG (\gamma=5), samples after epoch 5" loading="lazy">
    <figcaption>Class-conditioned flow matching with CFG ($\gamma=5$), samples after epoch 5</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_epoch_10.png" alt="Class-conditioned flow matching with CFG (\gamma=5), samples after epoch 10" loading="lazy">
    <figcaption>Class-conditioned flow matching with CFG ($\gamma=5$), samples after epoch 10</figcaption>
  </figure>
</div>

Class conditioning greatly improves samples. After epoch 1, each column shows the requested digit, though some $4$s, $5$s, $6$s, and $9$s are distorted. By epoch 5 all 40 are legible; by epoch 10 the strokes are smoother, with natural variation in the $1$s, $2$s, $4$s, and $7$s. Class labels resolve ambiguity, and CFG sharpens the class-specific samples.

##### Removing the learning-rate scheduler

**Can I get rid of the exponential learning-rate scheduler and keep the same quality?** The scheduler decays the learning rate by $\gamma=0.1^{1/10}\approx0.79$ each epoch, from $10^{-2}$ to $10^{-3}$. I replace it with a constant $3\times10^{-3}$ and keep the other settings (batch size $64$, $D=64$, $10$ epochs, $p_{\text{uncond}}=0.1$, $T=300$, $\gamma=5$, same seed). This is near the geometric mean, $10^{-2}\cdot0.1^{1/2}\approx3.2\times10^{-3}$ and below $10^{-2}$. I do not tune it further.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_5_loss_no_scheduler.png" alt="Training loss of the class-conditioned UNet without the scheduler (constant learning rate 3\times10^{-3})" loading="lazy">
    <figcaption>Training loss of the class-conditioned UNet without the scheduler (constant learning rate $3\times10^{-3}$)</figcaption>
  </figure>
</div>

I sample the model without the scheduler using the same setup: 4 per digit, $T=300$, $\gamma=5$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_no_scheduler_epoch_1.png" alt="Class-conditioned flow matching without scheduler (\gamma=5), samples after epoch 1" loading="lazy">
    <figcaption>Class-conditioned flow matching <strong>without scheduler</strong> ($\gamma=5$), samples after epoch 1</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_no_scheduler_epoch_5.png" alt="Class-conditioned flow matching without scheduler (\gamma=5), samples after epoch 5" loading="lazy">
    <figcaption>Class-conditioned flow matching <strong>without scheduler</strong> ($\gamma=5$), samples after epoch 5</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_no_scheduler_epoch_10.png" alt="Class-conditioned flow matching without scheduler (\gamma=5), samples after epoch 10" loading="lazy">
    <figcaption>Class-conditioned flow matching <strong>without scheduler</strong> ($\gamma=5$), samples after epoch 10</figcaption>
  </figure>
</div>

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>Epoch</th><th>With scheduler (last-batch loss)</th><th>Constant lr $3\times10^{-3}$ (last-batch loss)</th></tr></thead>
  <tbody>
    <tr><td>1</td><td>0.1227</td><td>0.1101</td></tr>
    <tr><td>5</td><td>0.0822</td><td>0.0795</td></tr>
    <tr><td>10</td><td>0.1030</td><td>0.0788</td></tr>
  </tbody>
</table>

By epoch 10, the constant-learning-rate model matches the scheduled model's sample quality. Epoch 5 has faint background speckle, while the scheduled model is cleaner; the table's last-batch losses are only indicative.

## Things I learned

The main lesson from Part B is the centroid argument: a denoiser trained on pure noise predicts the training-set mean, not a digit. Flow matching works iteratively because each $x_t$ gives the next step some information. Time-only conditioning leaves the digit ambiguous; class conditioning and CFG resolve it.

Part A showed the same idea at image scale. In editing, $t_0$ balances preserving pixels with trusting the model; around $t_0=0.10$–$0.14$, my drawings kept their composition but became photo-like. CFG extrapolates along $v_c-v_u$, while anagrams combine rotated predictions and hybrids split low/high frequencies, connecting back to Project 2.