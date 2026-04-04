# AI Prompt Log - Jannatussakira Khondaker

## Entry 1: Environment & Git Setup
- **Target** Set up a clean VS Code workspace and connect to GitHub.
- **AI Assistance:** Used Gemini to resolve a "10,000 files" Git tracking error and to correctly structure the project folders.
- **Action Taken:** Created .gitignore to prevent dataset syncing and organized files into sharp/blurred subfolders.

## Entry 2: Initial Kernel Logic
*User Intent:* Create a mathematical kernel for linear motion blur based on blur_metadata.json.

*AI Suggestion:* Provided a Python function using numpy and cv2.getRotationMatrix2D.

*Human Review & Action:* I identified that the AI's initial code didn't handle odd vs. even kernel sizes correctly, which caused centering errors. I manually added the if size % 2 == 0: size += 1 logic to ensure a perfect center point for the PSF.

## Entry 3: The "Radius" vs. "Diameter" Debate
**User Intent:** Determine why the restoration was failing (PSNR ~19 dB).

**AI Suggestion:** The AI hypothesized that size: 7 in the metadata meant Radius (Length 15).

**Human Review & Action:** CRITICAL CORRECTION. I ran the suggested "Length 15" code and the PSNR crashed to 8.68 dB. I rejected the AI's hypothesis, reverted the code to "Length 7," and directed the search toward spatial alignment instead.

## Entry 4: Spatial Alignment & Phase Shift
**User Intent:**  Fix the sub-pixel misalignment between blurred and sharp images.

**AI Suggestion:** Suggested a "grid search" to test vertical and horizontal shifts using scipy.ndimage.shift.

**Human Review & Action:** I implemented the grid search and discovered that a Shift=(-1, 0) was the "magic key." This raised the PSNR from ~20 dB to 24.39 dB. I verified that this was a vertical phase shift likely caused by the synthetic blurring script's coordinate system.

## Entry 5: The Deep Learning Pivot

**User Intent:** Explore Deep Learning (NAFNet) to see if it could beat the 26.66 dB baseline.

**AI Suggestion:** Provided installation steps for the NAFNet GitHub repository.

**Human Review & Action:** The AI's installation steps failed due to basicsr compilation errors in Colab. I worked with the AI to develop a "Plan B" (adding local folders to sys.path), but ultimately decided to stick with the Classical Wiener Filter for the final report to better document the mathematical trade-offs and limitations required by the rubric.

## Entry 6: Batch Evaluation & Statistical Evidence
**User Intent:** Transition from single-image testing to a statistically significant batch (20 images) to calculate mean metrics.

**AI Suggestion:** Recommended a Python loop using tqdm to process a subset of images and save them to a new val2017_restored directory for Task 3 analysis.

**Human Review & Action:** Implemented the batch restoration script. This generated the final Mean Detection score (2.30) for the quantitative summary.

## Entry 7: The "Ringing" vs. "Machine Vision" Trade-off

**User Intent:** Investigate why restored images caused the YOLOv8 detector to return 0 detections despite appearing sharper.

**AI Suggestion:* Identified that the Wiener filter was creating "Ringing Artifacts" (Gibbs phenomenon) and suggested increasing the balance (K) parameter to suppress high-frequency noise.

**Human Review & Action:** Critical Optimization. Performed a parameter sweep. Discovered that while balance=0.01 produced a sharper image, it created noise that confused the detector. Manually tuned the parameter to 0.05, which recovered 6 objects in the sample image, confirming that visual sharpness must be balanced with semantic clarity for AI models.

## Entry 8: The "Data Starvation" Pivot (Task 4 & 5)
- **User Intent:** Evaluate the near-zero mAP metrics resulting from the initial YOLOv8 fine-tuning run.
- **AI Suggestion:** The AI analyzed the training logs and identified "Data Starvation" and "Catastrophic Forgetting." It explained that a 20-image dataset is too small for deep learning convergence and suggested scaling to a 1,200-image "Quality Run." To meet a 30-minute compute budget, the AI provided a script to bypass Google Drive I/O bottlenecks by copying files directly to the Colab local SSD.
- **Human Review & Action:** I verified the pipeline logic (0 compilation errors) and agreed with the data starvation assessment. I documented the 20-image failure as a necessary "Proof of Concept" and authorized the scale-up script to generate the final, statistically significant metrics for the Task 5 report.

## Entry 9: YOLOv8 Directory Architecture & Visualizing Metrics
- **User Intent:** Extract the final statistical curves (Precision-Recall, F1) and visual predictions to fulfill the Task 5 reporting requirements.
- **AI Suggestion:** The AI initially provided a script pointing to the main training directory. When the files were not found, the AI diagnosed a YOLOv8 architectural quirk (the library automatically routes standalone validation outputs to a separate `/runs/detect/val` directory instead of the project folder) and provided a multi-directory search script.
- **Human Review & Action:** I executed the updated search script, which successfully located the generated curves. I reviewed the validation batch predictions (`val_batch0_pred.jpg`) to visually confirm the model's performance and used this empirical evidence to write the "Failure Cases" analysis for the final report.