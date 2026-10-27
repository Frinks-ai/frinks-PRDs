# Image Enhancement — filter descriptions

Text shown in the info (i) card of each filter, in Vision Module Setup and in Edit Module Configurations on Edge.
Each card shows the filter name, the description below, the values it takes, and a before/after picture of sample
"ABC 123" print. Kernel size is Rows × Columns, odd numbers 1–15, default 5 × 5. Full guide with pictures:
[Image Filter Guide](https://claude.ai/artifact/3S5hj7dJaBNiTWtfhefu2m).

| Category | Filter | Description | Values | Picture |
|---|---|---|---|---|
| Colour and tone | Invert | Swaps dark and light: dark print turns light and the background turns dark. Use it when the next step expects the opposite. | None | Dark print, default |
| Colour and tone | Equalize | Spreads the greys over the full black-to-white range, giving faint print strong contrast. Grain and dust are boosted too. | None | Dark print |
| Colour and tone | Stretch | Makes the darkest 1% black and the brightest 1% white, stretching everything between. A gentler contrast boost than Equalize. | None | Dark print |
| Colour and tone | Clip | Caps greys below Low and above High, flattening glare and deep shadow. Follow it with Stretch to win the contrast back. | Low, High (0–255; default 40, 200) | Dark print, Low 100, High 160 |
| Colour and tone | Optical Density | A log scale that pulls detail out of dark areas; dark print comes out lighter than the background. The result is dim, so follow it with Stretch. | None | Dark print |
| Colour and tone | Greyscale Distance | Each pixel becomes how far its grey is from the Grey level. Set it to the background's grey to turn the background black and the print light. | Grey level (0–255; default 128) | Dark print, Grey level 200 |
| Thresholding | Binarize | Turns every pixel black or white with a threshold it picks itself. One threshold for the whole image, so even out the light first. | None | Dark print |
| Thresholding | Threshold Range | Pixels with a grey between Low and High turn white, all others black. Picks out print of a known darkness. | Low, High (0–255; default 0, 110) | Dark print, Low 0, High 70 |
| Smooth and blur | Low Pass | Replaces each pixel with the average of its box, softening grain and noise. A bigger box blurs the print too. | Kernel size | Dark print, 5 × 5 |
| Smooth and blur | Local Median | Replaces each pixel with the middle value of its box, removing specks while keeping edges sharper than Low Pass. | Kernel size | Dark print, 5 × 5 |
| Sharpen and edges | Sharpen | Makes outlines crisper by adding back the difference from the local average. A bigger box makes print bolder but sharpens grain too. | Kernel size | Dark print, 5 × 5 |
| Sharpen and edges | High Pass | Keeps only detail smaller than the box, on flat mid-grey, so uneven light disappears. Make the box bigger than the print strokes. | Kernel size | Dark print, 15 × 15 |
| Sharpen and edges | Gradient Full | Shows how sharply the grey changes in every direction: outlines turn light, flat areas black. | None | Dark print |
| Sharpen and edges | Gradient Horizontal | Shows only left-to-right changes: upright strokes light up, sideways lines vanish. | None | Dark print |
| Sharpen and edges | Gradient Vertical | Shows only top-to-bottom changes: flat strokes and sideways lines light up. | None | Dark print |
| Sharpen and edges | Edge Magnitude | The outlines of Gradient Full at double brightness, so faint edges show too. | None | Dark print |
| Shape clean-up | Erode | Grows dark areas: dark print gets bolder and its dots join up, but dark specks grow too. | Kernel size | Dark print, 3 × 3 |
| Shape clean-up | Dilate | Grows light areas: dark print gets thinner, light print gets bolder. | Kernel size | Dark print, 3 × 3 |
| Shape clean-up | Open | Removes light spots smaller than the box and joins dark dot-matrix dots into solid strokes. Too big a box merges characters. | Kernel size | Dark print, 5 × 5 |
| Shape clean-up | Close | Removes dark spots smaller than the box, such as dust. Too big a box erases thin dark print. | Kernel size | Dark print, 3 × 3 |
| Shape clean-up | Fill Dark Holes | Fills dark spots fully surrounded by lighter pixels, such as pits in light marking. It also fills the insides of A, B and 0. | None | Light marking |
| Shape clean-up | Fill Light Holes | Fills light spots fully surrounded by darker pixels, such as glare caught in dark print. | None | Dark print |
| Background and lighting | Top Hat | Keeps only light details smaller than the box, on flat black: uneven light disappears. For light print on a dark part. | Kernel size | Light marking, 15 × 15 |
| Background and lighting | Bot Hat | Keeps only dark details smaller than the box, shown light on flat black: uneven light disappears. For dark print on a light part. | Kernel size | Dark print, 15 × 15 |
| Background and lighting | Max (Top Hat/Bottom Hat) | Top Hat and Bot Hat together, so both light and dark small details show. For print that can be lighter or darker than the part. | Kernel size | Dark print, 9 × 9 |
| Background and lighting | Local Median Normalization | Removes slow changes in light and centres the picture on mid-grey, keeping the print. The usual first step before Binarize. | Kernel size | Dark print, 15 × 15 |
