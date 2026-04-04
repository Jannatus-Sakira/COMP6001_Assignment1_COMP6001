COMP6001: Image Restoration & Object Detection
Student: Jannatussakira Khondaker

Project: Assignment 1 (task1 and 2)

Project Overview
This repository contains the implementation of classical image restoration techniques (Wiener Filtering) and their evaluation against synthetic motion blur in the val2017 dataset. This work serves as the foundation for subsequent object detection analysis.

Setup & Installation
To reproduce these results, ensure you have a Python 3.x environment (or Google Colab) with the following dependencies:

Task 2: Restoration Methodology
For the deblurring phase, I implemented a modular Wiener Filter approach.

The Challenge
The val2017 deterministic blurred dataset presented a unique challenge: a very high baseline PSNR of 26.66 dB. In such high-fidelity images, standard deconvolution often introduces more noise than it removes.

Key Technical Insights
Kernel Precision: I utilized cv2.line with anti-aliasing to match the synthetic blur kernel.

The Phase Shift Discovery: Through iterative testing, I discovered a vertical spatial misalignment. By applying a Shift=(-1, 0), I was able to stabilize the PSNR at 24.39 dB.

Hyperparameter Tuning: I found that a balance (K) of 0.01 provided the best trade-off between edge sharpening and the Gibbs phenomenon (ringing artifacts).

Task 1: Version Control & Responsible AI
As per the assignment requirements, this project adheres to professional standards for reproducibility and ethical AI use.

Version Control Strategy
Branching: Development was conducted on a feature/wiener-restoration branch to keep the main branch stable.

Atomic Commits: I followed a descriptive commit convention (e.g., feat:, fix:, docs:) to document the evolution of the restoration script.

Responsible AI Attribution & Reflection
Tool Used: Gemini 3 

Nature of Assistance: The AI assisted in drafting the initial mathematical structure for the create_precise_kernel function and suggested the diagnostic "grid search" used to identify the 1-pixel vertical shift.

Human Oversight & Verification: During the process, the AI initially hypothesized that the metadata size: 7 referred to a radius. Through manual iterative testing and PSNR analysis, I identified this as incorrect and corrected the logic to a 7-pixel diameter. This "human-in-the-loop" verification was essential to achieving the final result.

Ethics & Bias: No private data was used. The val2017 (COCO) dataset was reviewed for general content, noting that it represents standard everyday objects with minimal inherent social bias for this specific task.

Limitations & Future Work
======================================================
While the current restoration achieved 24.39 dB, the baseline of 26.66 dB proved difficult to surpass using classical non-blind methods. This highlighted the sensitivity of PSNR to sub-pixel alignment and boundary ringing. In Task 3, I will evaluate if this "mathematically lower" but "visually sharper" image actually improves object detection performance.
======================================================