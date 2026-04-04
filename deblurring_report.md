1. The Kernel Interpretation Failure

The Issue: The blur_metadata.json provided a size: 7 parameter. Initial tests were ambiguous about whether this referred to Radius (total length 15) or Diameter (total length 7).

The Result: Testing the "Radius" interpretation (Length 15) resulted in a PSNR drop to 8.68 dB, effectively destroying the image.

The Lesson: Documentation for synthetic datasets often lacks a "Standard of Truth" for coordinate systems. We confirmed through iteration that size refers to total pixel length in this specific dataset.

2. The Spatial Alignment (Phase Shift) Problem

The Issue: Even with the correct angle and size, the restored images initially peaked at 19–21 dB (well below the 26.66 dB baseline).

The Discovery: Through a grid-search algorithm, we discovered a 1-pixel vertical shift (Shift=(-1, 0)) between the blurred input and the ground truth.

The Limitation: Classical deconvolution (Wiener/Richardson-Lucy) is mathematically intolerant of even a 0.5-pixel misalignment. While the image looked "sharper" to the eye, the PSNR metric penalized the spatial offset severely.

3. The "High Baseline" Sensitivity

The Issue: The val2017_blurred_deterministic dataset has a very high baseline quality (26.66 dB).

The Failure: Most classical restoration attempts introduced "ringing" artifacts (Gibbs phenomenon) at the image boundaries.

The Analysis: In high-quality synthetic images, the "noise" added by the restoration process often outweighs the "sharpening" benefit in the eyes of the PSNR metric. This highlights a fundamental trade-off: Visual clarity does not always equal mathematical similarity.

4. Implementation Complexity & Dependency Hell

The Issue: Attempting to use State-of-the-Art (SOTA) Deep Learning models (NAFNet).

The Failure: Repeated ModuleNotFoundError and basicsr compilation errors in the Google Colab environment.

The Conclusion: There is a significant computational and maintenance cost to Deep Learning. While more robust than classical filters, the "environment overhead" makes them difficult to deploy in constrained or poorly documented pipelines.