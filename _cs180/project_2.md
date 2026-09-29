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

## Overview

This project is about understanding images as 2D signals and seeing how convolution and frequency filtering can be used for edges, sharpening, hybrid images, and seamless blending. I started from implementing convolution myself, then used derivatives and Gaussian smoothing to build better edge detectors. In the second half, I worked with high and low spatial frequencies to sharpen images, create hybrid images, and finally blend different images together with Gaussian and Laplacian stacks.

## Part 1: Fun with Filters

### Part 1.1: Convolutions from Scratch!

#### 4 loops

[include the exact code as required]

We loop through the image pixel by pixel, then we iterate through the kernel. In the four-loop version, the outer two loops choose the output pixel location, while the inner two loops iterate over every element of the kernel. For each output pixel, I multiply the overlapping image values by the corresponding kernel values and add them together.

The basic operation can be written as

$$
O(i,j) = \sum_{m}\sum_{n} I(i-m,j-n)K(m,n),
$$

where $I$ is the image, $K$ is the filter, and $O$ is the output. The filter is flipped before applying the operation because convolution, under the strict mathematical convention, requires reversing the kernel in both dimensions.

#### 2 loops

2 loops we iterate through the image pixel by pixel, write an edge case handler that we only need to sum the multiplication the sliced 2d kernel with the sliced 2d window

Instead of explicitly looping over the kernel elements, I can use a sliced portion of the image and kernel and perform the element-by-element multiplication followed by `np.sum`. This keeps the two loops for the image coordinates while using NumPy for the inner matrix operation. Near an image boundary, the kernel extends outside the image, so I use an edge-case handler that only multiplies the overlapping portions.

Conceptually, for a kernel centered at $(i,j)$, I find the valid image window and the matching valid portion of the kernel. The output pixel is then

$$
O(i,j)=\sum_{(m,n)\in \text{valid overlap}} I(m,n)K'(m,n),
$$

where $K'$ is the flipped kernel. With zero padding, the part of the kernel outside the image is equivalent to multiplying by zeros.

#### What can you use for this section?

What can you use for this section? I am not entirely sure about this question. I did use np.flip to flip the kernel and use np.sum in the 2d loops for the element by element matrix multiplication.

I used NumPy operations only for my from-scratch implementations. `np.flip` handles the kernel reversal needed for convolution, while `np.sum` computes the sum of all element-wise products in the overlapping region. This is the main difference between my four-loop and two-loop approaches: both perform the same convolution mathematically, but the two-loop version lets NumPy handle the inner arithmetic.

#### Runtime analysis

Runtime analysis: 4 loops, where each is horizontal edge detector filter, vertical edge detector filter, box filter

| Filter | Runtime (s) |
|---|---:|
| Horizontal edge detector | 23.0980164000066 |
| Vertical edge detector | 25.615388899997924 |
| Box filter | 16.682653500000015 |

The average runtime of these three four-loop runs is about $21.80$ seconds. The box filter is faster in this particular test, but the important comparison is between the implementation strategies rather than between the three filters themselves.

Runtime analysis: 2 loops: where each is horizontal edge detector filter, vertical edge detector filter, box filter

| Filter | Runtime (s) |
|---|---:|
| Horizontal edge detector | 16.189378600001 |
| Vertical edge detector | 16.10461409999698 |
| Box filter | 15.917823700001 |

The average runtime drops to about $16.07$ seconds. This is about $21.8\,/\,16.1 \approx 1.36\times$ faster than the four-loop implementation in my measurements. The mathematical operation is the same, but using NumPy's array operations avoids executing the innermost arithmetic directly in Python.

Runtime analysis library given convolve2d function: where each is horizontal edge detector filter, vertical edge detector filter, box filter

| Filter | Runtime (s) |
|---|---:|
| Horizontal edge detector | 0.07488610000291374 |
| Vertical edge detector | 0.0729940000019269 |
| Box filter | 0.08236240000405814 |

The library implementation is dramatically faster, with an average of about $0.0767$ seconds. Compared with my two-loop implementation, the measured speedup is roughly $16.07/0.0767 \approx 209\times$. This illustrates why optimized numerical libraries are useful for image processing: the operation itself is simple, but doing billions of small Python-level operations is expensive.

#### Results

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_1_1/me_gray.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Input grayscale image</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_1/me_gray_box.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">9×9 box filter</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_1/me_gray_hor.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Horizontal derivative</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_1/me_gray_ver.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Vertical derivative</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Self image with box and finite-difference filters</p>

<div style="text-align:center; margin: 12px 0;">
  <img src="/images/cs180/proj2/output/Part_1_1/me_gray_box.jpg" style="width: 55%;">
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">9×9 box-filter result</p>

The box filter averages a local neighborhood. For a $9\times9$ box filter, each coefficient is

$$
K_{box}(m,n)=\frac{1}{81},
$$

so the filter replaces each pixel with the average of the $81$ values in its neighborhood. This smooths the image and removes some fine detail.

The derivative filters instead respond to changes between neighboring pixels. The horizontal and vertical outputs therefore emphasize different edge orientations. This also demonstrates an important distinction between smoothing and differentiation: the box filter averages nearby values, while the derivative filters measure local changes.

Keynote:

1. We need to center the filter so that the center of the filter is at the pixel we are looking at. and flip the filter to achieve convolution (using the strict mathematical convention)
2. We do convolution thats the same as convolve2d mode="same". Here we did zero padding

For the boundary, `mode="same"` keeps the output the same size as the input while still computing a result near the edge. My implementation uses zero padding, so values outside the image are treated as zero. This matters because the kernel is only partially supported near the border, which can make edge pixels look different from interior pixels.

<details>
<summary><strong>Code: my 4-loop and 2-loop convolution implementations</strong></summary>

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

The only functional correction I made to the notebook code above is the final `return output` in the two-loop function. Without it, the function finishes after assigning the output array but returns `None` to the caller.

</details>

### Part 1.2: Finite Difference Operator

I used `im = np.hypot(ver, hor)` to get the magnitude of the horizontal and vertical derivative

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/supplement/cameraman_hor.png" style="width: 100%;">
    <figcaption style="text-align: center;">$D_x$: horizontal derivative</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/supplement/cameraman_ver.png" style="width: 100%;">
    <figcaption style="text-align: center;">$D_y$: vertical derivative</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_2/cameraman_raw_edge.png" style="width: 100%;">
    <figcaption style="text-align: center;">Gradient magnitude before thresholding</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_2/cameraman_thres.png" style="width: 100%;">
    <figcaption style="text-align: center;">Binarized edge image</figcaption>
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

The result is useful because an edge is a location where image intensity changes quickly. A large derivative means a strong local change, and the gradient magnitude measures the overall strength of that change regardless of whether it is mainly horizontal or vertical.

Finally, I binarize the gradient magnitude by selecting a threshold $T$:

$$
E(i,j)=
\begin{cases}
1,&G(i,j)>T\\
0,&G(i,j)\le T.
\end{cases}
$$

The threshold is a qualitative tradeoff. A threshold that is too low keeps small changes and noise, while a threshold that is too high removes weak but real edges. I chose the threshold to suppress much of the noise while retaining the main edges in the cameraman image.


###
<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/supplement/cameraman_gauss_thres.png" style="width: 100%;">
    <figcaption style="text-align: center;">Binarized gradient magnitude after Gaussian smoothing</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_2/cameraman_thres.png" style="width: 100%;">
    <figcaption style="text-align: center;">Finite-difference binarized edge image</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Comparison of edge maps before and after Gaussian smoothing</p>
 Part 1.3: Derivative of Gaussian (DoG) Filter

I used kernel_1d = cv.getGaussianKernel(5, 1.0) kernel_2d = kernel_1d * kernel_1d.T to create a 2D Gaussian kernel.

The 2D Gaussian is separable, so taking the outer product of the 1D Gaussian vector with its transpose gives the full 2D filter. In general,

$$
G(x,y)=G_x(x)G_y(y),
$$

which is why the outer product works.

A Gaussian filter is a low-pass filter: it reduces rapid pixel-to-pixel changes before the derivative is taken. This means that small noisy variations are less likely to appear as strong edges.

[show mathematically that convolve with the image is the same as convolve with the filter]

The key mathematical idea is associativity of convolution. If $I$ is the image, $G$ is the Gaussian filter, and $D_x$ is the derivative filter, then

$$
D_x * (G * I) = (D_x * G) * I.
$$

The same applies in the vertical direction:

$$
D_y * (G * I) = (D_y * G) * I.
$$

Therefore, instead of first blurring the image and then applying the derivative, I can first combine the Gaussian and derivative filters into a single filter and convolve that result directly with the original image. This combined filter is the Derivative of Gaussian (DoG) filter.

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 19%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss.png" style="width: 100%;">
    <figcaption style="text-align: center;">Gaussian-smoothed cameraman</figcaption>
  </figure>
  <figure style="width: 19%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss_filtered_hor.png" style="width: 100%;">
    <figcaption style="text-align: center;">Gaussian + horizontal derivative</figcaption>
  </figure>
  <figure style="width: 19%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss_filtered_ver.png" style="width: 100%;">
    <figcaption style="text-align: center;">Gaussian + vertical derivative</figcaption>
  </figure>
  <figure style="width: 19%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_3/cameraman_gauss_filtered_filter.png" style="width: 100%;">
    <figcaption style="text-align: center;">Gradient result (filename-based identification)</figcaption>
  </figure>
</div>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_3/gauss_hor_kernal.png" style="width: 100%;">
    <figcaption style="text-align: center;">Horizontal DoG filter</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_1_3/gauss_ver_kernal.png" style="width: 100%;">
    <figcaption style="text-align: center;">Vertical DoG filter</figcaption>
  </figure>
</div>

The two methods provide the same images except on the edges due to zero padding being applied once vs. being applied twice.

The small differences around the borders are expected because the two implementations can expose the image boundary to padding at different stages. In the interior of the image, the associativity relationship predicts the same filtering result, while boundary handling can make the final pixels differ.

Compared with the original finite-difference result, the Gaussian-smoothed result should have fewer small noisy responses. The tradeoff is that very fine detail can also be removed because the Gaussian is intentionally suppressing high spatial frequencies before differentiation.

Keynote: When convolving the kernel with the Gaussian filter, make sure to use mode="full" to not lose details from the image.

Using a full convolution when constructing the combined DoG kernel preserves the complete support of the two filters before they are applied to the image. The resulting combined filter is larger than either filter alone because the convolution of two finite kernels increases their support.

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

For evaluation, pick a sharp image, blur it, and then try to sharpen it again. Compare the original and the sharpened image and report your observations.

After subtracting the original image by the sharpened blurred image, the original image have more high frequency information than the sharpened blurred image. This can be caused by the lost high-frequency information that cannot be recovered after the blur. Applying the sharpening mask boosts the high-frequency details remaining in the blurred image, but it can't recover the information that was lost.

This experiment shows an important limitation of sharpening. Sharpening is not a time machine: once the blur has removed high-frequency information, the missing information cannot be exactly reconstructed. The sharpening filter only increases the contrast of the high-frequency information that remains after blurring. As $\alpha$ increases, edges become more pronounced, but overly large values can also make noise and halos more visible.

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/taj_0.5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Taj, $\alpha=0.5$</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/taj_1.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Taj, $\alpha=1$</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/taj_2.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Taj, $\alpha=2$</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/taj_5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Taj, $\alpha=5$</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Effect of increasing the sharpening amount</p>

As the sharpening amount increases from $\alpha=0.5$ to $\alpha=5$, the high-frequency component contributes more strongly to the final image. The lower values make a subtler change, while the larger values emphasize fine detail much more aggressively. This provides a direct visual demonstration of the role of $\alpha$ in the equation above.

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_1/taj.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Original Taj Mahal</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/supplement/taj_blur.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Taj, Gaussian blurred</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/supplement/taj_high_freq.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Taj, high-frequency component</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Decomposing the Taj Mahal image into low and high spatial frequencies</p>


<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_1/lobos.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Lobos, original sharp image</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/lobos_blur_5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Lobos, blurred</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/lobos_5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Lobos, sharpened after blur</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/calacademy_5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">CalAcademy, sharpened</figcaption>
  </figure>
</div>
<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/redwood_5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Redwood, sharpened</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_1/yosemite_5.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Yosemite, sharpened</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Sharp → blur → sharpen-back experiment on Lobos, plus additional sharpening results</p>

The Lobos experiment is the direct evaluation requested in the project description: I start with the original sharp image, blur it, and then apply the sharpening operation to that blurred image. The sharpened result recovers some apparent edge contrast, but it does not become identical to the original because the blur has already removed high-frequency information.

Keynote: always do np.clip or scale the image to the range from 0 to 1 before converting the floating points ot integers and multiplying by 255. This would cause integer overflow for example, a -1 value would be 255, which would add a lot of random color and pepper noise

Because the sharpening equation can produce values below $0$ or above $1$, the result needs to be brought back into the valid image range before converting to `uint8`. Otherwise, negative values or values above the maximum can wrap or clip incorrectly during integer conversion. Using `np.clip(res,0,1)` before multiplying by $255$ is a safe way to handle this.

The supplement folder contains the Taj blurred and Taj high-frequency images used above, so the required decomposition is now explicitly shown.

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

#### Frequency-domain analysis

[Frequency domain of the high frequency, low frequency, and hybrid image]

We notice in the low frequency image, most of the information is gathered in the middle cross while in high frequency image, most of the image is not on the axes, but on the quarters

More precisely, after applying `fftshift`, low-frequency energy is concentrated near the center of the Fourier image because the center represents low spatial frequencies. High-frequency energy appears farther from the center. It does not have to be literally on the axes or in the quarters, but the important visual distinction is that the high-frequency component is farther from the center than the low-frequency component.

For the frequency-domain visualization, the magnitude is displayed on a logarithmic scale. A typical computation is

$$
F(u,v)=\log\left(\left|\operatorname{fftshift}(\operatorname{fft2}(I))\right|\right),
$$

which makes both very strong low-frequency values and weaker high-frequency values easier to see at the same time.

#### Cutoff frequency choice

cutoff frequency choice:

High freq: increase sigma -> more information, decrease sigma -> less information
Low frequency: increase sigma -> less information, decrease sigma -> more information

The intuition is that $\sigma$ controls the width of the Gaussian blur. A larger $\sigma$ removes more high frequencies from the low-pass image, which makes the low-pass result smoother. For the high-pass component $I-G_\sigma*I$, a larger $\sigma$ means more of the original image is treated as high-frequency detail because the blur removes a wider range of frequencies.

Based on this rule, I tuned it that whenever I have my glass on, its the high frequency image while if I have my glass off, its the low frequency image. (Roy’s criteria)

This criterion is based on the expected viewing distance. At close range, the fine details are available and the high-frequency image is easier to recognize. At farther distances, the image is effectively blurred by the limited resolution of the visual system, making the low-frequency interpretation more prominent.

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_2/cat.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Cat input</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_2/man.jpg" style="width: 100%;">
    <figcaption style="text-align: center;">Man input</figcaption>
  </figure>
  <figure style="width: 46%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/catman.png" style="width: 100%;">
    <figcaption style="text-align: center;">Cat + man hybrid</figcaption>
  </figure>
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Hybrid result for the cat/man pair (filename-based pairing)</p>

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_2/lian_headshot.png" style="width: 100%;">
    <figcaption style="text-align: center;">Lian</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_2/taffy_headshot.png" style="width: 100%;">
    <figcaption style="text-align: center;">Taffy</figcaption>
  </figure>
  <figure style="width: 46%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/taffy_lian.png" style="width: 100%;">
    <figcaption style="text-align: center;">Taffy + Lian hybrid</figcaption>
  </figure>
</div>

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_2/hu_ge.png" style="width: 100%;">
    <figcaption style="text-align: center;">Hu Ge</figcaption>
  </figure>
  <figure style="width: 23%; margin: 0;">
    <img src="/images/cs180/proj2/image/Part_2_2/pig.png" style="width: 100%;">
    <figcaption style="text-align: center;">Pig</figcaption>
  </figure>
  <figure style="width: 46%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/hu_pig.png" style="width: 100%;">
    <figcaption style="text-align: center;">Hu Ge + pig hybrid</figcaption>
  </figure>
</div>

These examples demonstrate how changing the input pair changes the visual effect, while the same high-pass/low-pass construction remains the same. The cat/man result is especially useful for showing the intended distance-dependent interpretation, while the other examples show that the technique is not limited to a single type of subject.

#### Full frequency analysis example

For the Lian/Taffy example, the filenames indicate an aligned-input pair and separate frequency-domain visualizations. I use this as the main process example because it has the largest set of intermediate results.

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/aligned_lian.png" style="width: 100%;">
    <figcaption style="text-align: center;">Aligned Lian</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/aligned_taffy.png" style="width: 100%;">
    <figcaption style="text-align: center;">Aligned Taffy</figcaption>
  </figure>
</div>

<div style="display: flex; gap: 10px; margin-bottom: 6px; flex-wrap: wrap;">
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/lian.png" style="width: 100%;">
    <figcaption style="text-align: center;">Lian Fourier magnitude</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/taffy.png" style="width: 100%;">
    <figcaption style="text-align: center;">Taffy Fourier magnitude</figcaption>
  </figure>
  <figure style="width: 31%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/high.png" style="width: 100%;">
    <figcaption style="text-align: center;">High-frequency spectrum</figcaption>
  </figure>
</div>

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/low.png" style="width: 100%;">
    <figcaption style="text-align: center;">Low-frequency spectrum</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_2/freq_domain/hybrid.png" style="width: 100%;">
    <figcaption style="text-align: center;">Hybrid spectrum</figcaption>
  </figure>
</div>

The low-frequency Fourier magnitude should show its strongest energy near the center, while the high-frequency spectrum should move the visible energy farther from the center. The hybrid spectrum contains contributions from both, which is the frequency-domain counterpart of adding the high-frequency information from one image to the low-frequency information from the other.

<!-- MISSING/VERIFY: the rubric asks for the original and aligned images for the one fully analyzed hybrid; these are present for Lian/Taffy above. -->

## Part 2.3: Gaussian and Laplacian Stacks

[display the result]

A Gaussian stack and Laplacian stack keep the same image dimensions at every level. This is the same implementation as Project 1 except there is no downsampling.

I use gaussian_stack(img, sigma, levels=4), laplacian_stack(g) where level is the number of layers and sigma is the standard deviation of the Gaussian distribution of the filter. The output order of gaussian_stack is would be original, blurred once, blurred twice … fine detail, …, coarsest/general shape. The output order of laplacian_stack is bandpass filter 1 (greatest detail), bandpass filter 2 (less, but still great detail), … the same last layer of the Gaussian stack.

For the Gaussian stack, let $G_0=I$ and define successive levels as

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

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_3/graphs/laplacian_gaussian.png" style="width: 100%;">
    <figcaption style="text-align: center;">Gaussian/Laplacian stack visualization</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_3/graphs/oraple.png" style="width: 100%;">
    <figcaption style="text-align: center;">Oraple stack result / Figure 3.42-style visualization</figcaption>
  </figure>
</div>

The useful property of the Laplacian stack is that it gives me a way to manipulate different frequency bands separately. Instead of cutting an image at one location and accepting a hard boundary, I can later blend corresponding frequency bands with a smoothly varying mask.

The implementation is deliberately a stack rather than a pyramid: no level is downsampled. This means every level can be directly combined with a same-size mask and with the corresponding level from another image.

## Part 2.4: Multiresolution Blending (a.k.a. the oraple!)

The goal here is to use the Gaussian and Laplacian stacks to create a smooth transition between two images rather than making a visibly abrupt cut.

For a binary vertical or horizontal seam, the mask $M$ starts as a step function. For example, with a vertical seam,

$$
M(x,y)=
\begin{cases}
1,&x<x_0\\
0,&x\ge x_0.
\end{cases}
$$

The important step is that I also create a Gaussian stack of the mask. Let $M_i$ be the Gaussian-blurred mask at level $i$. Then for the corresponding Laplacian bands $L^A_i$ and $L^B_i$, I blend them using

$$
L_i^{blend}=M_i\,L^A_i+(1-M_i)\,L^B_i.
$$

Finally, the blended image is reconstructed by summing the blended Laplacian levels and the final low-frequency residual.

This works because the mask changes slowly at coarse frequency bands. A hard step in the original mask becomes a smooth transition after Gaussian filtering, so the seam is not equally sharp at every frequency.

Fixing Seam: I used two ways to fix seam:

1. Laplacian pyramid sigma*2**level for each level. This captures a broader range and makes coarser levels capture more detail and make the bandpass spam a larger spam of the spectrum instead of making all low frequency structure fit in the residual image
2. Making the Gaussian mask filter instead of a binary filter for the finest detail layer solves the seam issue significantly

The first idea makes the effective blur scale grow with the level, so the coarser bands represent broader spatial structures. The second is especially important for eliminating the obvious hard boundary: instead of multiplying the finest-level details by a binary left/right switch, a smoothly varying Gaussian mask gradually transfers detail from one image to the other.

### Oraple

<div style="text-align: center; margin-bottom: 6px;">
  <img src="/images/cs180/proj2/output/Part_2_3/oraple.jpeg" style="width: 70%;">
</div>
<p style="text-align:center; font-style: italic; margin-top: 0;">Apple + orange multiresolution blend</p>

The Oraple is the classic demonstration because the straight seam can be made visually smooth even though the left and right halves originally come from different images. The low-frequency bands transition gradually, while the Laplacian bands preserve local texture and edge information around the seam.

### Custom blend 1: Half-peeled shrimp

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_4/half_peeled_shrimp.jpeg" style="width: 100%;">
    <figcaption style="text-align: center;">Half-peeled shrimp blend</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_4/half_peeled_shrimp.png" style="width: 100%;">
    <figcaption style="text-align: center;">Half-peeled shrimp blend, alternate output</figcaption>
  </figure>
</div>

This example shows how the same multiresolution blending idea can be used outside the textbook apple/orange example. The mask determines which parts of each input contribute to the final result, while the Gaussian stack of the mask softens the transition.

### Custom blend 2: Hamster + Dafu

<div style="display: flex; gap: 10px; margin-bottom: 6px;">
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_4/hamster_dafu.jpeg" style="width: 100%;">
    <figcaption style="text-align: center;">Hamster + Dafu blend</figcaption>
  </figure>
  <figure style="width: 48%; margin: 0;">
    <img src="/images/cs180/proj2/output/Part_2_4/hamster_dafu.png" style="width: 100%;">
    <figcaption style="text-align: center;">Hamster + Dafu blend, alternate output</figcaption>
  </figure>
</div>

The custom examples demonstrate the more creative side of multiresolution blending. A useful mask does not have to be a straight vertical line. With an irregular mask, different parts of the two images can interleave, and the multiscale process helps the boundaries look more natural than a hard pixel-level cut.

<!-- MISSING/VERIFY: explicitly show the irregular mask itself for the custom example. The filenames alone cannot prove which custom blend uses the irregular mask. -->

Keynote:

Cv reads in BGR order while plt plot in RGB order. You have to reverse the color channels otherwise you would get color flipped image!

This matters whenever I move an image between OpenCV and Matplotlib. OpenCV conventionally loads color images as BGR, while Matplotlib expects RGB for display. If I forget to reverse the channels, the output can have visibly incorrect colors even though the underlying filtering operation is correct.

## Part 2.4: Results and Creative Exploration

The custom results also helped show why the Gaussian mask is useful. With a binary mask, the transition occurs at one abrupt boundary. With a Gaussian stack, the transition width changes with scale: fine details transition relatively locally, while coarse structures transition over a wider spatial region. This makes the seam much less noticeable.

The output images also show that multiresolution blending is more than simply averaging two pictures. Each spatial frequency band can be controlled independently, which gives much more natural-looking combinations when the two source images have compatible structure.

## Things I learned

What I amazed me the most about is the concept about breaking an image into a frequency domain. I always think of only 2d signals being able to break into frequency domain. Seeings how human eyes is able to depict high frequency (fine details) and low frequency (general shape) really fascinates me.

The most important connection for me was seeing the same idea appear repeatedly across the entire project. Derivatives emphasize changes, Gaussian filters remove high frequencies, Laplacian differences isolate frequency bands, and multiresolution blending combines those bands in a controlled way. What initially looked like several unrelated image-processing tricks turned out to be different uses of the same underlying idea: separating and manipulating spatial frequencies.

## Image Sources

The custom-source notes from my draft are:

- 自制盐豆大福 自从在日本吃过后就一直念念不忘 黑豆微... https://xhslink.cn/o/8MZ1LTafNCk
- 把小老鼠擀成饺子皮需要几步 - https://xhslink.cn/o/6EXZZQvzBg4
- https://x.com/watabieni/status/1759877676658233570
- https://xhslink.cn/m/4HHanyJsyJ

