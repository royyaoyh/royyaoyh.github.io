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

This project is about flow models: generative models that learn a velocity field which carries Gaussian noise to clean images. In **Part A** I do not train anything. I use a pretrained text-to-image flow model, PixNerd-XXL-P16-T2I (512×512 output), and implement the sampling machinery around it: building noisy inputs, comparing classical, one-step, and iterative denoising, generating images from pure noise, classifier-free guidance (CFG), SDEdit-style image editing, visual anagrams, and hybrid images. In **Part B** I build the whole pipeline from scratch on MNIST: a UNet single-step denoiser first, then a time-conditioned and a class-conditioned flow matching model trained with the same recipe, with CFG at sampling time.

Throughout the project I use the **same time convention**: $t=0$ is pure noise and $t=1$ is a clean image, and the network predicts a *velocity*, not noise.

## Part A: Exploring the Power of Flow Models

Setup for every Part A experiment: PixNerd-XXL-P16-T2I with the Qwen3 text encoder, float32, random seed $180$, Euler step size $h=0.02$, and CFG weight $w=7$ whenever guidance is used. The unconditional branch of CFG always uses the empty prompt `""`. No gradients are needed in Part A, so everything runs under `torch.inference_mode()`.

<div class="p3-note" markdown="1">

**Flow matching recap (Part A conventions)**

Linear interpolation between Gaussian noise $\epsilon\sim\mathcal{N}(0,I)$ and a clean image $x_1$:

$$
x_t=(1-t)\,\epsilon+t\,x_1,\qquad t\in[0,1].
$$

Training teaches the network $v_\theta(x_t,t,c)$ to predict the path velocity $x_1-\epsilon$ for a condition $c$ (a text prompt here):

$$
\mathcal{L}(\theta)=\mathbb{E}\left[\left\|v_\theta(x_t,t,c)-(x_1-\epsilon)\right\|_2^2\right].
$$

Sampling starts at $x_0=\epsilon$ and follows the learned ODE to $t=1$:

$$
\frac{dx_t}{dt}=v_\theta(x_t,t,c).
$$

</div>

### Section 1: Visualizing Training and Sampling

#### Choosing my image

For Sections 1.1–1.5 I use one image of my own (a photo of Changbai Mountain and its crater lake, Heaven Lake) instead of the Campanile. The photo loaded sideways, so I rotated it with `torch.rot90(image, k=3, dims=(-2,-1))` before using it. The supplied preprocessing center-crops and resizes it to $512\times512$ with RGB in $[-1,1]$.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_0_original.png" alt="My image (Changbai Mountain, Heaven Lake), after preprocessing to 512\times512" loading="lazy">
    <figcaption>My image (Changbai Mountain, Heaven Lake), after preprocessing to $512\times512$</figcaption>
  </figure>
</div>

#### 1.1 Constructing Noisy Training Inputs

Given a clean image $x_1$ and a Gaussian noise sample $\epsilon$, the noisy input at time $t$ is

$$
x_t=(1-t)\,\epsilon+t\,x_1 .\tag{A.1}
$$

This is **not** just adding noise: the image itself is scaled by $t$, and the noise by $1-t$. The two endpoints are $x_{0}=\epsilon$ (pure noise) and $x_{1}=x_1$ (clean image), which I verify with assertions in the notebook (`interpolate(image, eps, 0) == eps` and `interpolate(image, eps, 1) == image`). The amplitudes tell us how much image there is compared with noise: the signal-to-noise ratio of $x_t$ is

$$
\mathrm{SNR}(t)=\frac{t^2}{(1-t)^2}.
$$

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>$t$</th><th>image amplitude $t$</th><th>noise amplitude $1-t$</th><th>$\mathrm{SNR}(t)$</th></tr></thead>
  <tbody>
    <tr><td>0.25</td><td>0.25</td><td>0.75</td><td>1/9 ≈ 0.11</td></tr>
    <tr><td>0.50</td><td>0.50</td><td>0.50</td><td>1</td></tr>
    <tr><td>0.75</td><td>0.75</td><td>0.25</td><td>9</td></tr>
  </tbody>
</table>

I sampled **one** noise tensor $\epsilon$ (`noise(SEED)`, seed $180$) and reused it for all three times, so the three noisy images differ only through $t$. I saved the original image, $\epsilon$, and the three noisy tensors for Sections 1.2–1.5 (Section 1.4 needs the original image/noise pair for the ground-truth velocity).

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_1.png" alt="Original image and the noisy versions at t=0.25, t=0.50, and t=0.75" loading="lazy">
    <figcaption>Original image and the noisy versions at $t=0.25$, $t=0.50$, and $t=0.75$</figcaption>
  </figure>
</div>

Larger $t$ keeps more of the image: at $t=0.75$ the scene is clearly visible under mild noise, at $t=0.50$ the large structures are still readable, and at $t=0.25$ the noise dominates (the noise amplitude is three times the image amplitude).

#### 1.2 Classical Denoising

As a baseline without any learned model, I use a Gaussian blur. Averaging neighbouring pixels partially cancels independent zero-mean noise, but it also removes image detail. Because the image is scaled by $t$ inside $x_t$, I first restore its amplitude and then blur:

$$
\frac{x_t}{t}=x_1+\frac{1-t}{t}\,\epsilon,\qquad \hat{x}_1^{\mathrm{blur}}=G_\sigma\!\left(\frac{x_t}{t}\right).
$$

Dividing by $t$ fixes the image amplitude, but it does **not** remove noise. In fact it amplifies the noise to a standard deviation of $\frac{1-t}{t}$, which is $3$, $1$, and $\tfrac13$ for $t=0.25,\,0.50,\,0.75$. So the baseline has a much harder job at small $t$.

For each noisy input I swept $\sigma\in\{1,2,4,8,16\}$ (kernel size $6\sigma+1$) and kept the $\sigma$ with the lowest mean squared error to the known clean image. This is an *oracle* baseline: it peeks at the ground truth to choose $\sigma$, so a real classical denoiser would do no better than this. The chosen $\sigma$ is printed in each panel title.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_2.png" alt="Noisy input (left) and best Gaussian-denoised result (right) for t=0.25, t=0.50, t=0.75" loading="lazy">
    <figcaption>Noisy input (left) and best Gaussian-denoised result (right) for $t=0.25$, $t=0.50$, $t=0.75$</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): confirm these observations against 1_2.png -->

The blur trades noise for detail. When the noise is strong ($t=0.25$) even the best blur is a large one, so what is left is a smooth colour layout with no texture; when the noise is mild ($t=0.75$) a small $\sigma$ already works, but it still softens fine detail such as the rocks and the ice on the lake. A Gaussian filter cannot distinguish noise from fine image texture, since both are high-frequency, and that is the limit of this baseline.

#### 1.3 One-Step Denoising

Now I use the pretrained flow model. `predict_velocity(x, t, condition)` returns the predicted velocity $v_\theta(x_t,t,c)$. If the velocity were exactly $x_1-\epsilon$ and constant along the path, one step to $t=1$ would be exact:

$$
x_t+(1-t)(x_1-\epsilon)=(1-t)\epsilon+t x_1+(1-t)x_1-(1-t)\epsilon=x_1 .
$$

So the one-step clean-image estimate is

$$
\hat{x}_1=x_t+(1-t)\,v_\theta(x_t,t,c).\tag{A.2}
$$

The network predicts a **velocity**, not noise, so the prediction is added with the factor $(1-t)$ rather than subtracted. I use the prompt `"a high quality photo"` and check that the returned velocity has the same shape, dtype, and device as the input and is finite.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_3.png" alt="Original, noisy input, and one-step estimate for t=0.25, t=0.50, t=0.75" loading="lazy">
    <figcaption>Original, noisy input, and one-step estimate for $t=0.25$, $t=0.50$, $t=0.75$</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): confirm against 1_3.png -->

The one-step estimate is a much better denoiser than the blur, because the network has learned what natural images look like, but it is still soft. This is expected: the trained velocity at $(x_t,t)$ approximates the conditional mean $\mathbb{E}[x_1-\epsilon\mid x_t]$, so $x_t+(1-t)v_\theta$ approximates the posterior mean $\mathbb{E}[x_1\mid x_t]$, an average over all clean images consistent with the noisy one. When the noisy input is ambiguous ($t=0.25$) that average is blurry and lacks fine detail; at $t=0.75$ the posterior is narrow and the estimate is closer to the original.

#### 1.4 Visualizing Velocity

In Section 1.3 we added $(1-t)\hat v_t$ to $x_t$. Here I visualize what the model adds and compare it with what *should* be added. Since we know the original image and the noise, the exact update is $x_1-x_t$:

$$
v_{\mathrm{gt}}=x_1-\epsilon,\qquad (1-t)\,v_{\mathrm{gt}}=x_1-x_t,\qquad \hat v_t=v_\theta(x_t,t,c).
$$

The per-pixel error heatmap is the RMS difference of the two updates across the RGB channels (squared channel differences, averaged over RGB, then square root, computed **before** any display scaling or clipping):

$$
E_t(i,j)=\sqrt{\operatorname{mean}_{\mathrm{RGB}}\!\left(\Big[(1-t)\big(\hat v_t-v_{\mathrm{gt}}\big)\Big]^2\right)} .
$$

For display, both updates use the same symmetric RGB scale (zero is mid-gray), and the three error maps share the same color limits, so the three times are directly comparable.

The original image (shown once) is the one from the start of this section.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_0_original.png" alt="Original image (shown once)" loading="lazy">
    <figcaption>Original image (shown once)</figcaption>
  </figure>
</div>

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

<!-- CHECK (Roy): confirm 1_4_1/2/3 are t=0.25/0.50/0.75 and the observations below against the images -->

Two things stand out. First, both updates shrink as $t$ grows, because they carry the factor $(1-t)$: at $t=0.75$ only a quarter of the velocity is added. Second, the predicted update reproduces the large-scale structure of the ground-truth update (sky versus ground, the dark rocks), but not its exact fine-grained pattern. The ground-truth update contains the *specific* noise realization $\epsilon$ that was mixed in, which the network cannot know exactly, so it predicts the part that is consistent with natural images. That is why the error is concentrated on fine texture and edges rather than on smooth regions.

#### 1.5 Iterative Denoising with Euler Sampling

One-step denoising is inaccurate because the learned velocity **changes** along the path: the predicted direction depends on the current image and time, so the real sampling trajectory is curved. Instead of one big step I take many small Euler steps and recompute the velocity after each one:

$$
x_{t+h}=x_t+h\,v_\theta(x_t,t,c).\tag{A.3}
$$

With start time $t_0$ and step size $h=0.02$, the times are $t_k=t_0+kh$ and the number of steps is $N=\lceil (1-t_0)/h\rceil$. The last step is shortened, $h_k=\min(h,\,1-t_k)$, so the integration ends exactly at $t=1$. For $t_0=0.5$ this is $25$ updates; for $t_0=0$ it is $50$.

**Test with a constant velocity field.** For $v\equiv 1$ the exact answer after integrating from $t_0$ to $1$ is $x=x_{\text{start}}+(1-t_0)$. I check this on a toy tensor, including a start time that needs a shortened last step. The assertions pass for $t_0=0$ (50 full steps), $t_0=0.5$ (25 steps), and $t_0=0.93$ (4 steps, the last one only $0.01$ long).

Starting from the saved $t=0.50$ noisy image I run the $25$ Euler updates with `"a high quality photo"`. The figure shows the initial state, every fifth update, and the final image, labeled by time ($t=0.50,\,0.60,\,0.70,\,0.80,\,0.90,\,1.00$).

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_5.png" alt="Euler denoising trajectory from t=0.50 to t=1.00 (initial state, every fifth update, final image)" loading="lazy">
    <figcaption>Euler denoising trajectory from $t=0.50$ to $t=1.00$ (initial state, every fifth update, final image)</figcaption>
  </figure>
</div>

Now the final Euler result compared with the original, the one-step estimate (Section 1.3), and the Gaussian blur (Section 1.2), all from the **same** $t=0.50$ noisy input:

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_5_comp.png" alt="Original, Gaussian blur, one-step estimate, and Euler denoising from the same t=0.50 input" loading="lazy">
    <figcaption>Original, Gaussian blur, one-step estimate, and Euler denoising from the same $t=0.50$ input</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): confirm against 1_5.png / 1_5_comp.png -->

The trajectory first settles the large structures (sky, horizon, the lake) and only later adds fine detail, which matches the picture of a velocity field that moves from noise toward the data manifold. The iterative result is the sharpest and most natural of the three denoisers: the Gaussian blur is soft everywhere, and the one-step estimate is better but still averaged. Because every Euler step re-evaluates the velocity at the current, less noisy image, the network gets to commit to details as the ambiguity decreases, instead of having to predict the posterior mean from the very noisy $t=0.50$ input in one shot.

#### 1.6 Generating Images from Noise

The same `euler_sample` also generates images from scratch: I set $t_{\text{start}}=0$ and pass pure Gaussian noise as `x_start`, which means denoising pure noise with $50$ Euler updates. I use the prompt `"a high quality photo"` and five independent initial noises (seeds $180$–$184$, kept so that Section 2.1 can reuse **exactly the same noises**).

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_1/1_6.png" alt="Five samples with the prompt 'a high quality photo' (no guidance), seeds 180–184" loading="lazy">
    <figcaption>Five samples with the prompt "a high quality photo" (no guidance), seeds 180–184</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): name the subjects you actually see in 1_6.png, and whether they look washed out/soft -->

The generic prompt does not specify any subject, so the model falls back on whatever it most associates with "a high quality photo" and the five seeds show a narrow range of subjects and a plain, soft look instead of confident, well-composed pictures. The images are plausible but not very sharp or well aligned with the prompt, which is what CFG improves in the next section.

### Section 2: Conditional Generation and Guidance

#### 2.1 Classifier-Free Guidance (CFG)

To improve prompt alignment and image quality, at the expense of diversity, I use classifier-free guidance. At every step I evaluate the model twice on the **same** $x_t$ and $t$: once with the text prompt ($v_c$) and once with the empty-string prompt ($v_u$). Then I extrapolate from the unconditional prediction past the conditional one:

$$
v_{\mathrm{cfg}}=v_c+w\,(v_c-v_u).\tag{A.4}
$$

$w=0$ is ordinary conditional sampling. I use $w=7$ and $h=0.02$. Equivalently, $v_{\mathrm{cfg}}=v_u+(1+w)(v_c-v_u)$, so the guided velocity moves $1+w$ times as far in the "prompt direction" $v_c-v_u$.

Here are five CFG samples with `"a high quality photo"`, using the same initial noises as Section 1.6 (seeds $180$–$184$). For direct comparison, the unguided samples from Section 1.6 are shown first.

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

<!-- CHECK (Roy): confirm the comparison against 2_1.png -->

With the same starting noise, CFG changes the pictures substantially: they become sharper, higher in contrast and saturation, and more clearly "a high quality photograph" of a recognizable subject. This is the quality and prompt-alignment gain that guidance is designed to give. The cost is diversity: because every step is pushed $1+w=8$ times as far along $v_c-v_u$, the five samples are pushed toward the same kind of prototypical, well-lit photo and look more alike than the unguided ones, and large $w$ can also oversaturate colors.

### Section 3: Creative Applications

In this section I use flow sampling to edit existing images and to make optical illusions. Throughout, I use Euler step size $h=0.02$, CFG weight $w=7$, and the empty prompt `""` for the unconditional prediction.

#### 3.1 Image-to-Image Editing

This is the SDEdit idea. Take a real image $x_1$, noise it only up to some starting time $t_0$, and then run the guided sampler from $t_0$ to $1$:

$$
x_{t_0}=(1-t_0)\,\epsilon+t_0\,x_1,\qquad x_{t+h}=x_t+h\,v_{\mathrm{cfg}}(x_t,t,c)\quad\text{for } t=t_0,\dots,1 .
$$

The model has to "hallucinate" whatever the noise destroyed, so it effectively pushes the noisy image back onto the manifold of natural images. A **smaller** $t_0$ means more noise and a bigger edit; a **larger** $t_0$ keeps more of the original. I reuse the same $\epsilon$ (seed $180$) for every starting time, so only $t_0$ changes across a row. The starting times are $t_0\in\{0.02,0.04,0.06,0.10,0.14,0.25,0.50\}$ and the prompt is `"a high quality photo"`.

How much of the original is left in the starting image at each $t_0$ (the SNR is $t_0^2/(1-t_0)^2$ as in Section 1.1, and the number of Euler updates is $\lceil(1-t_0)/h\rceil$):

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <thead><tr><th>start time $t_0$</th><th>SNR of $x_{t_0}$</th><th>Euler updates</th></tr></thead>
  <tbody>
    <tr><td>0.02</td><td>0.0004</td><td>49</td></tr>
    <tr><td>0.04</td><td>0.0017</td><td>48</td></tr>
    <tr><td>0.06</td><td>0.0041</td><td>47</td></tr>
    <tr><td>0.10</td><td>0.0123</td><td>45</td></tr>
    <tr><td>0.14</td><td>0.0265</td><td>43</td></tr>
    <tr><td>0.25</td><td>0.1111</td><td>38</td></tr>
    <tr><td>0.50</td><td>1.0000</td><td>25</td></tr>
  </tbody>
</table>

So for $t_0\le 0.06$ the starting image holds well under $1\%$ of signal power compared with noise, and the sampler is nearly generating from scratch; only at $t_0=0.50$ do image and noise have equal power.

**Campanile.**

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/edit_campanile.png" alt="Campanile, prompt 'a high quality photo', starting times t_0=0.02\dots0.50" loading="lazy">
    <figcaption>Campanile, prompt "a high quality photo", starting times $t_0=0.02\dots0.50$</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): this file is not in your proj3 folder listing (campanile edit sweep); the name edit_campanile.png is what the notebook saves. Update the path/describe what you see. -->

As $t_0$ increases the output matches the Campanile more and more closely: with very little of the image left the model produces an unrelated photo that only keeps a vague color or shape, the tower and its layout come back in the middle range, and by $t_0=0.50$ the edit is close to the original.

**Two of my own test images.** The first is a plate with a pancake and blueberries held in one hand (`pancake`); the second is a cup of dessert held in one hand, which I rotated by $90^\circ$ clockwise before use (`yopo`).

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/pancake_edit_my_image_1.png" alt="My image 1 (pancake plate), prompt 'a high quality photo'" loading="lazy">
    <figcaption>My image 1 (pancake plate), prompt "a high quality photo"</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/yopo_edit_my_image_2.png" alt="My image 2 (cup of dessert), prompt 'a high quality photo'" loading="lazy">
    <figcaption>My image 2 (cup of dessert), prompt "a high quality photo"</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): describe the pancake_edit_my_image_1.png sweep from the actual file (I could only see the yopo sweep). -->

For the dessert cup, the start of the sweep shows how little the model keeps from a nearly pure-noise input: at $t_0=0.02$ the sampler generates a close-up of a man's face, at $t_0=0.04$ and $0.06$ it keeps only the hand and invents a tablet-like object, at $t_0=0.10$ and $0.14$ it keeps the hand and some of the colors (the yellow of the tactile paving, the green of the spoon, a pink block) but not the cup, and from $t_0=0.25$ the cup with its pink lid is recognizable again. At $t_0=0.50$ the result is almost the input.

#### Editing hand-drawn and web images

This procedure works best on non-realistic inputs (drawings, paintings), because the edit projects them onto the manifold of natural images. Here I use one image from the web and two of my own hand drawings, with the same seven starting times.

**Web image.**

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/moon_lord_edit_web_image.png" alt="Web image (Moon Lord), prompt 'a high quality photo'" loading="lazy">
    <figcaption>Web image (Moon Lord), prompt "a high quality photo"</figcaption>
  </figure>
</div>

<!-- CHECK (Roy): describe moon_lord_edit_web_image.png from the actual file -->

**Hand drawing 1: Golden Gate Bridge.** At $t_0=0.02$ the drawing is gone entirely and the sampler produces a portrait of a smiling woman in a cap. At $t_0=0.04$ and $0.06$ the only thing that survives is the color layout: blue and pink blocks rearranged into a hand-held device and a lamp. At $t_0=0.10$ and $0.14$ the model turns my flat drawing into a **photograph of a bridge**: the tower, the cables, and the red-brown color are kept, and clouds and soft shading are added. For $t_0\ge0.25$ the flat drawing style returns (with a slightly textured look), so the edit is subtle.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/golden_gate_edit_drawing_1.png" alt="Hand drawing 1 (Golden Gate), prompt 'a high quality photo'" loading="lazy">
    <figcaption>Hand drawing 1 (Golden Gate), prompt "a high quality photo"</figcaption>
  </figure>
</div>

**Hand drawing 2: Yosemite.** Again $t_0=0.02$ gives an unrelated image (a woman sitting on a couch), and at $t_0=0.04$ and $0.06$ the model builds a modern building in a landscape from the grey and green blocks. At $t_0=0.10$ and $0.14$ the drawing becomes a **realistic photograph of a mountain valley**: the granite walls, the sky, and the forest are all rendered as photo textures, while a few traces of my drawing remain (white patches and a black bar, the latter even turned into scribbled lettering). From $t_0=0.25$ it is back to the flat drawing.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/yosemite_edit_drawing_2.png" alt="Hand drawing 2 (Yosemite), prompt 'a high quality photo'" loading="lazy">
    <figcaption>Hand drawing 2 (Yosemite), prompt "a high quality photo"</figcaption>
  </figure>
</div>

The sweet spot for turning a drawing into a photo is around $t_0=0.10$–$0.14$: enough of the drawing remains to fix the composition, but enough noise has been added that the model re-renders surfaces as photographic textures. Below that the model ignores the drawing, above it the model just trusts the pixels.

**Extra experiments** (same procedure): the `hajimi` cat image and a Van Gogh painting.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/cat_edit_drawing_3.png" alt="Extra: cat image, prompt 'a high quality photo'" loading="lazy">
    <figcaption>Extra: cat image, prompt "a high quality photo"</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/van_gogh_edit_drawing_4.png" alt="Extra: Van Gogh painting, prompt 'a high quality photo'" loading="lazy">
    <figcaption>Extra: Van Gogh painting, prompt "a high quality photo"</figcaption>
  </figure>
</div>

#### 3.2 Text-Guided Image Editing

Same procedure as 3.1, but the generic prompt is replaced by a prompt that describes the **desired edit**. The prompt guides how the model fills in the noise, so now the starting time decides the balance between *the prompt* (small $t_0$) and *the original image* (large $t_0$).

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

<!-- CHECK (Roy): describe text_edit_campanile.png from the actual file -->

Small $t_0$ lets the prompt win and the output is unrelated to the Campanile; large $t_0$ lets the tower win. In between, the clock-tower silhouette is kept while the prompt re-styles it toward the target landmark.

**Pancake plate to a seafood feast.** The prompt rewrites the plate completely. At $t_0=0.02$ to $0.06$ the original is gone and the result is an unrelated seafood platter. At $t_0=0.10$ and $0.14$ the **composition of my photo is kept** (a round plate seen from above on a brown table, forks at the top left, the plate in the middle) and the food on it becomes a lobster, mussels, and lemon wedges. At $t_0=0.25$ the plate rim with its blue-and-white pattern comes back and the food becomes a mix of pancake-colored and seafood elements with steam rising, and at $t_0=0.50$ the pancake with blueberries is basically back with only a few seafood elements (a claw and dark mussels) mixed in.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/pancake_seafood_text_edit_my_image_1.png" alt="My image 1 (pancake), prompt: seafood feast on a large platter" loading="lazy">
    <figcaption>My image 1 (pancake), prompt: seafood feast on a large platter</figcaption>
  </figure>
</div>

**Dessert cup to a man with pink and green hair.** For $t_0\le0.06$ the result is a portrait of a man with pink and green hair that has nothing to do with the cup, but at $t_0=0.10$ and $0.14$ it keeps the **colors and the blurred background** of my photo (grey ground, the yellow strip) and the pink and green of the original (pink dessert, green spoon) show up as the hair. At $t_0=0.25$ the pink dessert has turned into a pink head of hair growing out of the cup, with the green spoon still visible, and at $t_0=0.50$ the cup is back and the swirled dessert only has a pastel pink-green tint.

<div class="p3-grid c1 ">
  <figure>
    <img src="/images/cs180/proj3/part_a/part_3/yopo_man_text_edit_my_image_2.png" alt="My image 2 (dessert cup), prompt: 'male with pink and green hair'" loading="lazy">
    <figcaption>My image 2 (dessert cup), prompt: "male with pink and green hair"</figcaption>
  </figure>
</div>

The comparison with Section 3.1 on the same image is the instructive part: with the generic prompt the dessert cup is lost for $t_0\le0.14$ and the model invents a face, a tablet, and a fist; with a prompt that mentions pink and green, the same colors from the photo are *used* to build the target. The text prompt turns the model's "creativity" into a direction instead of leaving it random.

#### 3.3 Visual Anagrams

A visual anagram is a single image that shows one subject upright and a different subject when rotated by $180^\circ$. I build it by denoising $x_t$ with prompt $p_1$ and, at the same time, denoising the **rotated** $x_t$ with prompt $p_2$, then rotating the second velocity back and averaging:

$$
v_1=v_{\mathrm{cfg}}(x_t,t,p_1),
$$

$$
v_2=\operatorname{flip}\!\Big(v_{\mathrm{cfg}}\big(\operatorname{flip}(x_t),\,t,\,p_2\big)\Big),
$$

$$
v=\frac{v_1+v_2}{2},\qquad x_{t+h}=x_t+h\,v .
$$

Here $\operatorname{flip}(\cdot)$ is a $180^\circ$ rotation, i.e., it flips **both** spatial axes (not just a vertical mirror), so $\operatorname{flip}(\operatorname{flip}(x))=x$ (which I assert in the notebook). Why it works: $\operatorname{flip}$ only permutes pixels, so the flipped noisy image is still a valid noisy image (Gaussian noise is unchanged by permuting pixels), and the network can denoise it with the second prompt. Averaging forces both views to agree on one image: each Euler step moves $x_t$ in a direction that increases the likelihood of $p_1$ when seen upright and the likelihood of $p_2$ when seen rotated.

Each figure below shows the **same** generated image upright (left, prompt $p_1$) and rotated by $180^\circ$ (right, prompt $p_2$). I tried several prompt and seed combinations, because a numerically successful run does not guarantee that both subjects are recognizable.

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

The **old man / campfire** illusion works best: the glowing forehead and hair of the old man become the fire, and his dark robes and shoulders become the people leaning in around it, so both readings are clear and the oil-painting style is consistent. The **angel / demon** pair works because both are frontal, roughly symmetric figures, so the angel's head and outstretched wings land on the demon's horns and arms after rotation. The **village / horse** and **Chinese / European village** pairs are weaker: in the horse view the strokes of the mountain and the houses are visible as a faint overlay, and the two village scenes are similar enough that the difference is mostly in the roof shapes. In general it helps when the two subjects have similar layouts and similar overall color and tone, and the style words (`oil painting`, `lithograph`, `watercolor`) keep both views from diverging.

#### 3.4 Hybrid Images

Here I adapt Factorized Diffusion to make hybrid images like those of Project 2: a picture that looks like one thing from far away and another thing up close. I predict two guided velocities with two prompts and combine the **low frequencies** of one with the **high frequencies** of the other:

$$
v_1=v_{\mathrm{cfg}}(x_t,t,p_1),\qquad v_2=v_{\mathrm{cfg}}(x_t,t,p_2),
$$

$$
v=f_{\mathrm{lowpass}}(v_1)+f_{\mathrm{highpass}}(v_2),\qquad f_{\mathrm{highpass}}(v)=v-f_{\mathrm{lowpass}}(v),
$$

$$
x_{t+h}=x_t+h\,v .
$$

$f_{\mathrm{lowpass}}$ is a Gaussian blur with kernel size $265$ and $\sigma=16$ at $512\times512$ (the kernel size is $2\cdot\mathrm{round}(16.5\cdot512/64)+1=265$). Because the blur is linear, the update splits cleanly into frequency bands: the coarse structure of $x$ follows prompt $p_1$ (what you see from far away), and the fine detail follows prompt $p_2$ (what you see up close). Prompt $p_1$ is therefore the "distant" prompt and $p_2$ the "close" prompt.

Each hybrid is shown at its **full size** ($512\times512$, the close view) and again at a **reduced size** ($64\times64$, the distant view, where the high frequencies are no longer visible).

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

<!-- CHECK (Roy): confirm church_temple_hybrid_0.png is the temple/church pair (cell 46) and not the "chinese temple" variant from cell 47; if it is the variant, change the temple prompt text above. The 64 px view is the browser downscaling the same file; the notebook used Lanczos. -->

Hybrid 1 mostly shows its close prompt: at full size it is a dark, furry **dog** head, and at $64\times64$ what survives is a dark silhouette with pointed ears, which can be read as a cat, but the cat reading is weak because the low-frequency band of a black silhouette is just a dark blob. Hybrid 2 is a more architectural pair: the church dominates both views, and the temple is hard to see in the reduced version. I generated both with several attempts. The high-frequency prompt tends to win in practice, since line-art detail has strong contrast, so the most successful hybrids have two subjects whose **coarse silhouettes are different** but share a similar tone.

## Part B: Flow Matching from Scratch!

In Part B I train my own flow matching model on MNIST. I start with a simple one-step denoiser to see why it is not enough for generation, then train a UNet that predicts the flow and generate digits by iterative denoising, first conditioned on time only and then on time and class with classifier-free guidance. All Part B models are trained on the 60,000-image MNIST **training** set only, with pixels in $[0,1]$, fresh noise sampled in every step, and seed $180$ on a Tesla T4 GPU.

### Part 1: Training a Single-Step Denoising UNet

Given a noisy image $z$ I want a denoiser $D_\theta$ that maps it to the clean image $x$, trained with an L2 loss:

$$
L=\mathbb{E}_{z,x}\left\|D_\theta(z)-x\right\|^2 .\tag{B.1}
$$

#### 1.1 Implementing the UNet

The denoiser is a UNet with two downsampling blocks, a bottleneck, two upsampling blocks, and skip connections (concatenations along the channel axis). The simple operations are built from standard layers, where `BN` is `nn.BatchNorm2d` and `GELU` is `nn.GELU`:

- **Conv**: `Conv2d(3, 1, 1)` + BN + GELU. Changes only the number of channels.
- **DownConv**: `Conv2d(3, 2, 1)` + BN + GELU. Halves the height and width.
- **UpConv**: `ConvTranspose2d(4, 2, 1)` + BN + GELU. Doubles the height and width.
- **Flatten**: `AvgPool2d(7)` + GELU. Turns a $7\times7$ map into $1\times1$.
- **Unflatten**: `ConvTranspose2d(7, 7, 0)` + BN + GELU. Turns a $1\times1$ map back into $7\times7$.
- **ConvBlock**: Conv → Conv. **DownBlock**: DownConv → ConvBlock. **UpBlock**: UpConv → ConvBlock. These only add depth and do not change the shape.

$D$ is the hidden width (a hyperparameter). With input $1\times28\times28$ the tensor shapes through the network are:

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

<details markdown="1">
<summary>Show full UNet code (simple ops, composed ops, unconditional UNet)</summary>

</details>

#### 1.2 Using the UNet to Train a Denoiser

To train the denoiser I need pairs $(z,x)$ where $x$ is a clean MNIST digit. I make $z$ from $x$ with

$$
z=x+\sigma\epsilon,\qquad \epsilon\sim\mathcal{N}(0,I),\tag{B.2}
$$

with $x\in[0,1]$. The figure shows the noising process for $\sigma\in\{0.0,0.2,0.4,0.5,0.6,0.8,1.0\}$: the digits become harder to read as $\sigma$ grows, and for $\sigma=1.0$ the noise has the same standard deviation as the full pixel range, so the digit is almost invisible.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2.png" alt="Noising process z=x+\sigma\epsilon for \sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0 (five digits)" loading="lazy">
    <figcaption>Noising process $z=x+\sigma\epsilon$ for $\sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0$ (five digits)</figcaption>
  </figure>
</div>

##### 1.2.1 Training

I train the denoiser to remove noise with **$\sigma=0.5$**:

<table class="p3-table" style="width:auto; display:table; margin:1em auto;">
  <tbody>
    <tr><td>Model</td><td>UNet, hidden dimension $D=128$</td></tr>
    <tr><td>Data</td><td>MNIST train set, shuffled, batch size 256 (235 iterations per epoch)</td></tr>
    <tr><td>Noise</td><td>$z=x+0.5\,\epsilon$, a <strong>fresh</strong> $\epsilon$ every time a batch is fetched, so each epoch sees new noisy versions</td></tr>
    <tr><td>Loss / optimizer</td><td>MSE (B.1), Adam, learning rate $10^{-4}$, no scheduler</td></tr>
    <tr><td>Epochs</td><td>5</td></tr>
  </tbody>
</table>

**Training loss curve.** The loss is logged at every iteration (faint line) and a 100-iteration moving average is overlaid. It drops very fast in the first ~100 iterations and then decreases slowly. The last-batch loss after each epoch is $0.0120,\ 0.0101,\ 0.0092,\ 0.0091,\ 0.0085$ for epochs $1$ to $5$.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_1_loss_graph.png" alt="Training loss (MSE) of the \sigma=0.5 denoiser over all 5 epochs" loading="lazy">
    <figcaption>Training loss (MSE) of the $\sigma=0.5$ denoiser over all 5 epochs</figcaption>
  </figure>
</div>

**Results on the test set** (fixed test digits and fixed noise so the two epochs are comparable). Each figure has three rows: clean digit, noisy input ($\sigma=0.5$), and denoised output.

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

After one epoch the denoiser already recovers all the digits, with some residual grain and soft edges. After five epochs the strokes are smoother and the background is cleaner, which agrees with the lower loss.

##### 1.2.2 Out-of-Distribution Testing

The denoiser only saw $\sigma=0.5$ during training. Here I keep the **same** test digits and the **same** noise direction $\epsilon$ and only change the noise level $\sigma\in\{0.0,0.2,0.4,0.5,0.6,0.8,1.0\}$ (so $z=x+\sigma\epsilon$ for a fixed $\epsilon$), and denoise with the trained model.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_2.png" alt="Denoiser trained at \sigma=0.5, tested at \sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0 (noisy rows and denoised rows)" loading="lazy">
    <figcaption>Denoiser trained at $\sigma=0.5$, tested at $\sigma=0.0,\,0.2,\,0.4,\,0.5,\,0.6,\,0.8,\,1.0$ (noisy rows and denoised rows)</figcaption>
  </figure>
</div>

The denoiser generalizes in one direction better than in the other. For **smaller** noise ($\sigma\le0.5$) the digits are recovered cleanly, even for $\sigma=0.2$ and $0.4$, which it never saw. For $\sigma=0.0$ (a clean input) the output is a little blurry, because the model is used to removing noise and applies the same smoothing. For **larger** noise ($\sigma=0.6$ and above) it degrades: at $\sigma=0.6$ the digit is thinner and broken, and at $\sigma=0.8$ and $1.0$ the network produces stroke-like artifacts and partially hallucinated shapes, because the input contains far more noise than it was trained to handle and it can no longer tell which of the strokes are real.

##### 1.2.3 Denoising Pure Noise

To turn denoising into a generative task, I feed **pure noise** $z=\epsilon\sim\mathcal{N}(0,I)$ and ask for a clean digit. I repeat the training from 1.2.1 (same $D=128$ UNet, Adam $10^{-4}$, batch 256, 5 epochs), but the input is now pure noise and the target is still a clean digit.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_1_2_3_loss_graph.png" alt="Training loss of the pure-noise denoiser over all 5 epochs" loading="lazy">
    <figcaption>Training loss of the pure-noise denoiser over all 5 epochs</figcaption>
  </figure>
</div>

The loss is flat: it drops quickly during the first ~100 iterations and then stays at about $0.067$ for the rest of training (last-batch losses $0.0679,\ 0.0685,\ 0.0654,\ 0.0741,\ 0.0679$), much higher than the $0.0085$ of the $\sigma=0.5$ denoiser.

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

**What patterns do I observe?** Every output is **the same blurry blob**, regardless of which noise was given. It does not look like any single digit: it looks like a smeared mixture of many digits, with a bright center and a vague ring or "8/0"-like shape. After epoch 5 it is smoother than after epoch 1, but it is the same image.

**Why?** The pure-noise input $z$ is independent of the digit $x$, so it carries no information about which digit to draw. With an MSE loss the best the network can do for any input is to output the point that minimizes the total squared distance to all training images. For a fixed input $z$ that is

$$
D^*(z)=\arg\min_f\ \mathbb{E}_x\|f-x\|^2=\mathbb{E}[x\mid z]=\mathbb{E}[x]=\bar{x}.
$$

So the optimal output is the **mean (centroid) of the training set**: the pixel-wise average of all digits $0$–$9$. That image is bright where many digits have ink (the center), faint at the edges, and has no sharp strokes, because the strokes of different digits cancel each other out. The flat loss curve fits this too: the loss plateaus at the error of always predicting the mean, $\mathbb{E}\|x-\bar{x}\|^2$ per pixel, which cannot be reduced without knowing the digit. A single-step denoiser therefore cannot generate: when the input has no information, it just regresses to the average. The fix is to do it gradually, so that every step starts from an input that carries a bit more information about the digit, which is exactly what flow matching does in Part 2.

### Part 2: Training a Flow Matching Model

One-step denoising does not work for generation, so I **denoise iteratively** with flow matching. I sample pure noise $x_0\sim\mathcal{N}(0,I)$ and want to generate a realistic image $x_1$. For a clean training image $x_1$ the noisy sample at time $t$ is the linear interpolation

$$
x_t=(1-t)\,x_0+t\,x_1,\qquad x_0\sim\mathcal{N}(0,I),\quad t\in[0,1].\tag{B.3}
$$

For small $t$ we are close to noise and for large $t$ close to the data. The **flow** (velocity) of this path is how the position changes with time:

$$
u(x_t,t)=\frac{d}{dt}x_t=x_1-x_0.\tag{B.4}
$$

The UNet $u_\theta(x_t,t)$ is trained to approximate it, with the objective

$$
L=\mathbb{E}_{x_0\sim p_0,\ x_1\sim p_1,\ t\sim U[0,1]}\left\|(x_1-x_0)-u_\theta(x_t,t)\right\|^2.\tag{B.5}
$$

Note that although the network *looks* like it maps a noisy image to a clean image, it is **not** predicting the clean image: it predicts the flow $x_1-x_0$, which contains both the image content and the noise to remove. A given $x_t$ can come from many pairs $(x_0,x_1)$, so the minimizer of (B.5) is the conditional mean $\mathbb{E}[x_1-x_0\mid x_t,t]$, the average flow through $(x_t,t)$; integrating this field from noise gives samples.

#### 2.1 Adding Time Conditioning to UNet

The network needs to know $t$, because the same $x_t$ requires a different velocity at different times. I inject the scalar $t$ with a fully connected block, **FCBlock**: `Linear(F_in, F_out)` → `GELU` → `Linear(F_out, F_out)`. Because $t$ is a scalar, $F_{in}=1$, and I always normalize $t$ into $[0,1]$ before embedding it.

<div class="p3-grid c2 ">
  <figure>
    <img src="/images/cs180/proj3/part_b/fc_long.png" alt="FCBlock: Linear(F_{in},F_{out}) + GELU, then Linear(F_{out},F_{out})" loading="lazy">
    <figcaption>FCBlock: Linear($F_{in},F_{out}$) + GELU, then Linear($F_{out},F_{out}$)</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/conditional_arch_fm.png" alt="Time-conditioned UNet: two FCBlocks inject t (red box) into the unflatten and the first up-block" loading="lazy">
    <figcaption>Time-conditioned UNet: two FCBlocks inject $t$ (red box) into the unflatten and the first up-block</figcaption>
  </figure>
</div>

The embeddings **modulate** the decoder features by channel-wise multiplication. With $t_1=\mathrm{FCBlock}_1(t)\in\mathbb{R}^{2D}$ and $t_2=\mathrm{FCBlock}_2(t)\in\mathbb{R}^{D}$ (each reshaped to $2D\times1\times1$ and $D\times1\times1$ so they broadcast over the spatial dimensions):

$$
\text{unflatten}'=\text{unflatten}\odot t_1,\qquad \text{up1}'=\text{up1}\odot t_2 .
$$

<details markdown="1">
<summary>Show full code (FCBlock and TimeConditionalUNet)</summary>

</details>

#### 2.2 Training the UNet

Training the time-conditioned UNet $u_\theta(x_t,t)$ is Algorithm B.1: pick a clean image $x_1$ from the training set, a random $t\sim U[0,1]$ and noise $x_0\sim\mathcal{N}(0,I)$, build $x_t$ with (B.3), and take a gradient step on the squared error between the predicted flow and $x_1-x_0$. In my implementation every sample in a batch gets its own $t$ and its own $x_0$.

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

The loss starts very high (above $2$ in the first few iterations at learning rate $10^{-2}$) and falls to about $0.09$–$0.10$ (last-batch losses $0.1445$ after epoch 1, $0.0944$ after epoch 5, and $0.0961$ after epoch 10). It does not go to zero, which is expected: for a given $x_t$ the target $x_1-x_0$ is not unique (many pairs lead to the same $x_t$), and the network can only learn the average.

#### 2.3 Sampling from the UNet

To sample I use Algorithm B.2: start from pure noise $x_0$ and take $T=50$ Euler steps from $t=0$ to $t=1$ with step size $1/T$, evaluating the network at the current $x_t$ and $t$ in every step:

$$
x_{t+1/T}=x_t+\frac{1}{T}\,u_\theta(x_t,t),\qquad t=0,\tfrac{1}{T},\dots,\tfrac{T-1}{T}.
$$

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/algo2_t_only_fm.png" alt="Algorithm B.2: sampling from the time-conditioned UNet" loading="lazy">
    <figcaption>Algorithm B.2: sampling from the time-conditioned UNet</figcaption>
  </figure>
</div>

I saved checkpoints after epochs $1$, $5$, and $10$ and sample $40$ digits from each with the **same** seed (so the same $40$ starting noises are used and the grids are comparable).

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_3_epoch_1.png" alt="Time-conditioned flow matching, samples after epoch 1" loading="lazy">
    <figcaption>Time-conditioned flow matching, samples after epoch 1</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_3_epoch_5.png" alt="Time-conditioned flow matching, samples after epoch 5" loading="lazy">
    <figcaption>Time-conditioned flow matching, samples after epoch 5</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_3_epoch_10.png" alt="Time-conditioned flow matching, samples after epoch 10" loading="lazy">
    <figcaption>Time-conditioned flow matching, samples after epoch 10</figcaption>
  </figure>
</div>

After **epoch 1** the samples are mostly broken or fragmented strokes with a lot of noisy gray in the background, and only a few look like digits. After **epoch 5** most of the samples are recognizable digits with clean backgrounds, although many of them are distorted. After **epoch 10** the strokes are cleaner and more consistent and several digits are quite legible, but some samples are still malformed (broken loops, merged strokes, or shapes between two digits). This is the limitation of a model that is conditioned on time only: it has no way to say *which* digit to draw, so when the trajectory starts in between two digit modes it can end on a blend of them.

#### 2.4 Adding Class-Conditioning to UNet

To get better results and control over which digit is generated, I also condition on the digit class $c\in\{0,\dots,9\}$. I use a **one-hot** vector for $c$ and add two more FCBlocks (with $F_{in}=10$), $c_1=\mathrm{FCBlock}_1(c)\in\mathbb{R}^{2D}$ and $c_2=\mathrm{FCBlock}_2(c)\in\mathbb{R}^{D}$. Class and time embeddings together modulate the features:

$$
\text{unflatten}'=c_1\odot\text{unflatten}+t_1,\qquad \text{up1}'=c_2\odot\text{up1}+t_2 .
$$

I also want the network to work **without** a class, so that I can use classifier-free guidance later. During training, with probability $p_{\text{uncond}}=0.1$ the conditioning vector is dropped by setting $c$ to the **zero vector** (decided independently for every sample in the batch, using a mask in the forward pass).

<details markdown="1">
<summary>Show full code (ClassConditionalUNet)</summary>

</details>

#### 2.5 Training the UNet

Training is the same as the time-only version (Algorithm B.3), with two differences: the label $c$ is used as a one-hot condition, and with probability $p_{\text{uncond}}=0.1$ it is replaced by the zero vector, so the same network learns both a conditional flow $u_\theta(x_t,t,c)$ and an unconditional one $u_\theta(x_t,t,0)$.

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/algo3_c_fm.png" alt="Algorithm B.3: training the class-conditioned UNet" loading="lazy">
    <figcaption>Algorithm B.3: training the class-conditioned UNet</figcaption>
  </figure>
</div>

The hyperparameters are the same as for the time-only model (Section 2.2): $D=64$, batch size $64$, Adam with initial learning rate $10^{-2}$, `ExponentialLR` with $\gamma=0.1^{1/10}$ stepped once per epoch, $10$ epochs, $p_{\text{uncond}}=0.1$. Checkpoints are saved after epochs $1$, $5$, and $10$.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_5_loss_scheduler.png" alt="Training loss of the class-conditioned UNet (with the exponential learning-rate scheduler)" loading="lazy">
    <figcaption>Training loss of the class-conditioned UNet (with the exponential learning-rate scheduler)</figcaption>
  </figure>
</div>

The loss has a large spike at the very beginning of training (the first iterations are around $12$ at learning rate $10^{-2}$) and then drops quickly to about $0.1$ (last-batch losses $0.1227$ after epoch 1, $0.0822$ after epoch 5, $0.1030$ after epoch 10; a single batch of 64 is noisy, so these numbers fluctuate). The curve is flat by eye after the first epoch, because most of the remaining improvement is in the sample quality (see 2.6), not in the average MSE.

#### 2.6 Sampling from the UNet

Sampling with the class-conditioned UNet is Algorithm B.4. At every one of the $T=300$ Euler steps I compute **both** the unconditional flow (class dropped) and the conditional flow (class $c$) at the same $(x_t,t)$ and combine them with classifier-free guidance of scale $\gamma=5.0$:

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

**Keynote: two CFG conventions.** Part A wrote guidance as $v_c+w(v_c-v_u)$ and Part B writes it as $u_u+\gamma(u_c-u_u)$. They are the same thing with $\gamma=1+w$, since $v_c+w(v_c-v_u)=v_u+(1+w)(v_c-v_u)$. So $\gamma=5$ here is the same as $w=4$ in the Part A convention, and $\gamma=1$ is plain conditional sampling.

</div>

I generate **4 instances of each digit** (a $4\times10$ grid; every row is $0,1,\dots,9$) with $300$ Euler steps, $\gamma=5$, and the same seed at each checkpoint, for epochs $1$, $5$, and $10$.

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

Class conditioning with guidance is a big improvement over the time-only model. After a **single epoch** every column already shows the requested digit, although a few are sloppy (some $4$s, $5$s, $6$s, and $9$s are distorted or have extra strokes). After **epoch 5** all 40 images are clean, legible digits of the right class, and after **epoch 10** the strokes are smooth and consistent, with natural variation in style (slant, thickness, and shape of the $1$s, $2$s, $4$s, and $7$s) inside each column. The class tells the network which mode to go to, so trajectories no longer end between two digits, and the guidance extrapolates away from the unconditional flow toward the class-specific one, which sharpens the strokes. This is why the class-conditioned model needs only 10 epochs to be much better than the time-only model after 10 epochs.

##### Removing the learning-rate scheduler

**Can I get rid of the exponential learning-rate scheduler and keep the same quality?** The scheduler multiplies the learning rate by $\gamma=0.1^{1/10}\approx0.79$ after every epoch, from $10^{-2}$ down to $10^{-3}$, so it gives large, fast steps at the start (when the model is far from good) and small, precise ones at the end. A constant learning rate has to be a compromise. I replaced it with a **constant learning rate of $3\times10^{-3}$** and left everything else the same (batch size $64$, $D=64$, $10$ epochs, $p_{\text{uncond}}=0.1$, $T=300$, $\gamma=5$, same sampling seed). I chose it as roughly the geometric mean of the scheduler's learning rates, $10^{-2}\cdot0.1^{1/2}\approx3.2\times10^{-3}$: it is smaller than $10^{-2}$ (which causes the large loss spike at the start of the scheduled run) but still large enough for the model to keep learning at the same pace for all 10 epochs. I used this one setting and did not tune it further.

<div class="p3-grid c1 narrow">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_5_loss_no_scheduler.png" alt="Training loss of the class-conditioned UNet without the scheduler (constant learning rate 3\times10^{-3})" loading="lazy">
    <figcaption>Training loss of the class-conditioned UNet without the scheduler (constant learning rate $3\times10^{-3}$)</figcaption>
  </figure>
</div>

Samples from the model trained without the scheduler, with the same sampling procedure (4 per digit, $T=300$, $\gamma=5$):

<div class="p3-grid c1 medium">
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_no_scheduler_epoch_1.png" alt="Class-conditioned flow matching **without scheduler** (\gamma=5), samples after epoch 1" loading="lazy">
    <figcaption>Class-conditioned flow matching **without scheduler** ($\gamma=5$), samples after epoch 1</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_no_scheduler_epoch_5.png" alt="Class-conditioned flow matching **without scheduler** (\gamma=5), samples after epoch 5" loading="lazy">
    <figcaption>Class-conditioned flow matching **without scheduler** ($\gamma=5$), samples after epoch 5</figcaption>
  </figure>
  <figure>
    <img src="/images/cs180/proj3/part_b/part_2_6_class_conditioned_no_scheduler_epoch_10.png" alt="Class-conditioned flow matching **without scheduler** (\gamma=5), samples after epoch 10" loading="lazy">
    <figcaption>Class-conditioned flow matching **without scheduler** ($\gamma=5$), samples after epoch 10</figcaption>
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

The final result is the same: after epoch 10 the digits without the scheduler are as clean and legible as the scheduled model, and the loss curve is just as flat. The differences are in the earlier epochs: with a constant learning rate the model is already decent after one epoch (thin, slightly ragged strokes), and after epoch 5 a faint gray speckle is still visible in the background of the images, because the learning rate never becomes small enough to settle the weights, while with the scheduler the background is already clean at epoch 5. So a well-chosen constant learning rate matches the final quality of the scheduler, at the price of slightly noisier intermediate checkpoints. (The last-batch losses in the table are single-batch values and only indicative.)

## Things I learned

The idea that stayed with me from Part B is the *centroid* argument. A one-step denoiser trained on pure noise has to output the mean of the training set, because under MSE the best answer for an uninformative input is the average, and seeing that blob after training makes it clear why generation has to be done iteratively. Flow matching works because every intermediate $x_t$ already contains a little information about the final digit, and each Euler step only needs to use that information to take a small step. The time-only model shows what is still missing (it does not know which digit to commit to), and class conditioning with classifier-free guidance fixes this, which I could literally see in the grids.

Part A showed me the same ideas at a much bigger scale. The starting time $t_0$ in image editing is a dial between *"trust the pixels"* and *"trust the model"*, and the interesting range (for me around $t_0=0.10$–$0.14$) is where the model keeps the composition and color layout of my drawing but re-renders the surfaces as a photograph. Classifier-free guidance is simply extrapolation along $v_c-v_u$, and the visual anagrams and hybrid images are just different ways of combining two guided velocities (averaging them in two different orientations, or splitting them by frequency band), so the connection to the frequency-domain ideas from Project 2 came back in the hybrids: the low-pass and high-pass split of the velocity decides which prompt you see from far away and which one up close.
