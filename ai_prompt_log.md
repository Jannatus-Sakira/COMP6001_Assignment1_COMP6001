# AI Prompt Log - Jannatussakira Khondaker

## Task 1: Environment & Git Setup
- **Target** Set up a clean VS Code workspace and connect to GitHub.
- **AI Assistance:** Used Gemini to resolve a "10,000 files" Git tracking error and to correctly structure the project folders.
- **Action Taken:** Created .gitignore to prevent dataset syncing and organized files into sharp/blurred subfolders.

## Task 2: Initial Kernel Logic
*User Intent:* Create a mathematical kernel for linear motion blur based on blur_metadata.json.

*AI Suggestion:* Provided a Python function using numpy and cv2.getRotationMatrix2D.

*Human Review & Action:* I identified that the AI's initial code didn't handle odd vs. even kernel sizes correctly, which caused centering errors. I manually added the if size % 2 == 0: size += 1 logic to ensure a perfect center point for the PSF.

## Task 3: The "Radius" vs. "Diameter" Debate
**User Intent:** Determine why the restoration was failing (PSNR ~19 dB).

**AI Suggestion:** The AI hypothesized that size: 7 in the metadata meant Radius (Length 15).

**Human Review & Action:** CRITICAL CORRECTION. I ran the suggested "Length 15" code and the PSNR crashed to 8.68 dB. I rejected the AI's hypothesis, reverted the code to "Length 7," and directed the search toward spatial alignment instead.

## Task 4: Spatial Alignment & Phase Shift
**User Intent:**  Fix the sub-pixel misalignment between blurred and sharp images.

**AI Suggestion:** Suggested a "grid search" to test vertical and horizontal shifts using scipy.ndimage.shift.

**Human Review & Action:** I implemented the grid search and discovered that a Shift=(-1, 0) was the "magic key." This raised the PSNR from ~20 dB to 24.39 dB. I verified that this was a vertical phase shift likely caused by the synthetic blurring script's coordinate system.

## Task 5: The Deep Learning Pivot

**User Intent:** Explore Deep Learning (NAFNet) to see if it could beat the 26.66 dB baseline.

**AI Suggestion:** Provided installation steps for the NAFNet GitHub repository.

**Human Review & Action:** The AI's installation steps failed due to basicsr compilation errors in Colab. I worked with the AI to develop a "Plan B" (adding local folders to sys.path), but ultimately decided to stick with the Classical Wiener Filter for the final report to better document the mathematical trade-offs and limitations required by the rubric.