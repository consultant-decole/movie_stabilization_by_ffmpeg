# stabilizing_drone_movies_by_ffmpeg

## Motivation

I realized that videos taken with toy drones or lightweight drones (under 100g) were often too shaky to enjoy or use effectively. When Google Colab introduced Gemini as an AI coding partner, I was able to develop this script through a collaborative dialogue with the AI. I'm sharing it here for anyone facing similar "shaky footage" problems.

[vidstab Documentation](https://adamspannbauer.github.io/python_video_stab/html/index.html)  
[vidstab on GitHub](https://github.com/AdamSpannbauer/python_video_stab)

This tool functions as a "software gimbal" to smooth out flight footage.

---

## Key Features

1. **Automatic Frame Rate Detection** Uses `ffprobe` to extract the exact average frame rate (FPS) from the input video, ensuring the output video maintains perfect synchronization.

2. **Two-Pass Stabilization** * **Step 1 (Detection):** Uses `vidstabdetect` to analyze camera motion and generate a temporary analysis file (`transform.trf`).
   * **Step 2 (Correction):** Uses the analyzed data to shift the video in the opposite direction of the shakes, effectively canceling them out.

3. **Canvas Expansion (Padding)** * Creates a canvas **1.4x larger** than the original video and centers the footage.
   * By using a black background (black borders) instead of zooming in, it prevents loss of image data even when the stabilized frame moves significantly.

4. **Redundancy Check** Automatically skips files that have already been processed, allowing for efficient batch processing.

---

## Technical Stack

* **Python 3**
* **FFmpeg** (Must be compiled with the `vid.stab` module enabled)
* **ffprobe** (For video metadata analysis)

---

## Workflow

| Step | Action | Details / Parameters |
| :--- | :--- | :--- |
| **0. Analysis** | Get FPS | `ffprobe -v error -select_streams v:0` |
| **1. Detection** | Analyze Shake | `vidstabdetect=shakiness=5:accuracy=15` |
| **2. Generation** | Stabilize & Expand | `vidstabtransform=optzoom=0` / `pad=iw*1.4:ih*1.4` |
| **3. Save** | High-Quality Output | `libx264`, `crf 18`, `preset slow` |

---

## Filter Details

Explanation of the key options used in the script:

* **`smoothing=20`**: Determines the smoothness of the correction. Higher values result in smoother motion but may cause larger shifts in the frame.
* **`optzoom=0`**: Disables automatic zooming. Since this is set to `0`, black borders will appear at the edges of the video instead of cropping.
* **`interpol=bicubic`**: The interpolation algorithm. Uses high-quality bicubic interpolation for pixel remapping.
* **`pad=iw*1.4:ih*1.4:(ow-iw)/2:(oh-ih)/2:black`**: Sets the output size to 140% of the original and centers the video.

---

## Notes

* **Temporary Files**: The `transform.trf` analysis file is automatically deleted after the process is complete.
* **Dependencies**: FFmpeg must be installed and accessible in your system's PATH.

---

## Preparation

* **Working Folder Setup**: Please create a `.env` file based on the `.env.blank` template (see the Execution Environment section below).

---

## Execution Environment

* **Directory Configuration**: Based on `.env.sample`, enter your input and output paths for the following variables. Rename the file to `.env` so the program can reference it.
* **Input Directory**: `SEARCH_DIR = "Path to your input videos"`
* **Output Directory**: `OUTPUT_DIR = "Path to save output videos"`

---

## Sample Result

* Below is a comparison with the input video on the left and the stabilized output on the right.

[View Comparison Video](https://github.com/consultant-decole/movie_stabilization_by_ffmpeg/issues/1#issue-3885884238)
