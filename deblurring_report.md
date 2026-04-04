## 1. The Kernel Interpretation Failure

The Issue: The blur_metadata.json provided a size: 7 parameter. Initial tests were ambiguous about whether this referred to Radius (total length 15) or Diameter (total length 7).

The Result: Testing the "Radius" interpretation (Length 15) resulted in a PSNR drop to 8.68 dB, effectively destroying the image.

The Lesson: Documentation for synthetic datasets often lacks a "Standard of Truth" for coordinate systems. We confirmed through iteration that size refers to total pixel length in this specific dataset.

## 2. The Spatial Alignment (Phase Shift) Problem

The Issue: Even with the correct angle and size, the restored images initially peaked at 19–21 dB (well below the 26.66 dB baseline).

The Discovery: Through a grid-search algorithm, we discovered a 1-pixel vertical shift (Shift=(-1, 0)) between the blurred input and the ground truth.

The Limitation: Classical deconvolution (Wiener/Richardson-Lucy) is mathematically intolerant of even a 0.5-pixel misalignment. While the image looked "sharper" to the eye, the PSNR metric penalized the spatial offset severely.

## 3. The "High Baseline" Sensitivity

The Issue: The val2017_blurred_deterministic dataset has a very high baseline quality (26.66 dB).

The Failure: Most classical restoration attempts introduced "ringing" artifacts (Gibbs phenomenon) at the image boundaries.

The Analysis: In high-quality synthetic images, the "noise" added by the restoration process often outweighs the "sharpening" benefit in the eyes of the PSNR metric. This highlights a fundamental trade-off: Visual clarity does not always equal mathematical similarity.

## 4. Implementation Complexity & Dependency Hell

The Issue: Attempting to use State-of-the-Art (SOTA) Deep Learning models (NAFNet).

The Failure: Repeated ModuleNotFoundError and basicsr compilation errors in the Google Colab environment.

The Conclusion: There is a significant computational and maintenance cost to Deep Learning. While more robust than classical filters, the "environment overhead" makes them difficult to deploy in constrained or poorly documented pipelines.

## 5. The Semantic Utility Paradox (Object Detection Failure) The Problem: 

At first, restoring with low balance parameter (K=0.01) generated the sharpest visual edges but resulted in the complete failure of the YOLOv8 object detector (0 detections on sample images).The Discovery: The artifacts (Gibbs phenomenon) of the discovery caused high-frequency oscillatory noise, which, although it seemed sharp to the human eye, served as an opaque to the feature extraction layers of the AI model.The Optimization: We deliberately blurred the restoration by adding the balance parameter up to 0.05. This reduced mathematical sharpness but recovered the semantic information, allowing the model to detect 6 objects in the sample image.The Lesson: The machine vision restoration goals need other optimization goals compared to the human consumption restoration. Reduced artifacts and a lower PSNR is better than increased artifacts and high-frequency artifacts (ringing).

## 6. Limitations of AI during the study 

The Observation: Though the modular framework of the Wiener filter and YOLOv8 was generated with the help of the generative AI systems, the systems first proposed the parameters that were technically correct but failed to work with the concrete dataset.

The Critical Action: It needed to use a manual intervention to determine the sub-pixel phase shift, and it needed to conduct the parameter sweep of the balance factor.

The Conclusion: The present project indicates that the concept of Automated Coding is merely a beginning rather than an end. The success of engineering relied on determining the location of the AI-generated logic that was dissimilar to the actual reality of the artificial dataset.

=====================================================

After 20-image failure as a deliberate test 
### In the 1st Phase (Micro Batch Proof of Concept - 20 images)

*we validate the end-to-end training pipeline, an initial fine-tuning run was executed on the 20-image subset for 10 epochs.*

**Micro-Batch Validation Metrics:**
* **Precision (P):** 0.000096
* **Recall (R):** 0.0152
* **Mean Average Precision (mAP50):** 0.00129

**Critical Analysis of Phase 1 Limitations:**
As anticipated, the near-zero metrics indicate severe *Data Starvation*. Object detection architectures like YOLOv8 require diverse, large-scale data to generalize. With only 20 images and 10 epochs, the model experiences "catastrophic forgetting"—it degrades its pre-trained COCO weights to fit the new data, but lacks the volume to learn the underlying semantic structures of the restored images. Instead, it likely overfits to the specific "ringing" artifacts of those 20 frames.

### Phase 2 Justification: High-Volume Scaling
To achieve functional detection parity and properly evaluate the advantages of restored imagery, the pipeline must be scaled. The next phase will expand the training set to **1,200 images** over **50 epochs**. To bypass cloud-storage I/O bottlenecks that would otherwise stall training, the dataset will be dynamically transferred to the local volatile NVMe storage of the compute instance prior to execution.