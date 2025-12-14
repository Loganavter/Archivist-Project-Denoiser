<p align="center"><strong>Archivist & Project Degrader</strong></p>

<p align="center">
  <strong>A comprehensive suite for cel animation restoration: specialized AI models and the physics-based degradation simulator used to train them.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Architecture-RealESRGAN_Compact-blue?style=flat-square">
  <img src="https://img.shields.io/badge/App-PyQt6-green?style=flat-square">
  <img src="https://img.shields.io/badge/Focus-Cel_Animation-orange?style=flat-square">
</p>

---

## 📚 Table of Contents
- [**Archivist Models**](#-archivist-models) (Download & Usage)
- [**Project Degrader**](#-project-degrader-software) (Dataset Generation Tool)

---

# 🎞️ Archivist Models

**Archivist** is a set of Real-ESRGAN Compact (48nf16nc) models trained to handle specific defects found in old cel animation (1940s-1980s), such as Metrocolor degradation, film tears, chemical stains, and emulsion shifts.

Unlike general-purpose denoisers, these models were trained on a **physically-simulated degradation pipeline** (see [Project Degrader](#-project-degrader-software) below), allowing them to distinguish between intended line art and physical damage.

### ⚙️ Technical Specs
*   **Architecture:** Real-ESRGAN Compact (SRVGGNetCompact)
*   **Config:** 48 filters, 16 blocks (48nf16nc)
*   **Scale:** 1x (Restoration/Denoising)

### 🧩 Model Zoo

| Model (Click to Download) | Iterations | Role & Best Use Case | Comparison |
| :--- | :--- | :--- | :--- |
| [**AntiLines**](https://github.com/Loganavter/Archivist-Project-Denoiser/releases/download/v1.0/Archivist_AntiLines.pth) | 457k | **The Cleaner.** Specifically targets **horizontal** lines, film tears, and scratches that cut through the frame. | [**View on ImgSLI**](https://imgsli.com/NDM0NTIy/0/1) |
| [**Rough**](https://github.com/Loganavter/Archivist-Project-Denoiser/releases/download/v1.0/Archivist_Rough.pth) | 493k | **The Rescuer.** For heavily damaged footage. Hallucinates lost details. | [**View on ImgSLI**](https://imgsli.com/NDM0NTI2/0/4) |
| [**Medium**](https://github.com/Loganavter/Archivist-Project-Denoiser/releases/download/v1.0/Archivist_Medium.pth) | 478k | **The Workhorse.** Balanced removal of grain and dirt while preserving original texture. The best starting point. | [**View on ImgSLI**](https://imgsli.com/NDM0NTE3/0/2) |
| [**Soft**](https://github.com/Loganavter/Archivist-Project-Denoiser/releases/download/v1.0/Archivist_Soft.pth) | 453k | **The Artist.** Gentle restoration. Keeps film grain aesthetic. <br>⚠️ **Note:** *In some scenarios, standard DRUNet might yield subjectively better results. Always compare.* | [**View on ImgSLI**](https://imgsli.com/NDM0NTI5/0/5) |
| [**RGB**](https://github.com/Loganavter/Archivist-Project-Denoiser/releases/download/v1.0/Archivist_RGB.pth) | 193k | **The Specialist.** Targets heavy chromatic noise and color channel degradation. *Note: overlaps partially with Rough.* | [**View on ImgSLI**](https://imgsli.com/NDM0NTI3/0/3) |

> **Legacy Models:** Older versions (BW/RGB Denoise Compact) are available in the `Archived_2024` folder.

### 🛠 Recommended Workflow

For "Hollywood-grade" results, use a **Two-Stage Pipeline**. Archivist models restore the structure, while a mathematical denoiser stabilizes the result.

1.  **Stage 1 (Restoration):** Process with **Archivist** to remove physical defects (scratches, lines, stains).
2.  **Stage 2 (Stabilization):** Process the result with **DRUNet** (low strength). This removes residual mathematical noise and stabilizes the video temporally.

### 🚀 Usage (REAL-Video-Enhancer)

The easiest way to use these models is via **[REAL-Video-Enhancer](https://github.com/TNTwise/REAL-Video-Enhancer)**, which supports TensorRT optimization and the DRUNet pipeline.

1.  Download the `.pth` files from this repository.
2.  In RVE, click **"Add Model"** and select the `.pth` file (it will convert to TensorRT automatically).
3.  Select the **Archivist** model as the main upscaler (1x).
4.  Enable **Denoise (DRUNet)** in the settings for stabilization.

---

# 🧪 Project Degrader Software

Located in the `Degrader/` folder, this is the **GUI application** written in Python (PyQt6) used to generate the training dataset for Archivist.

Standard noise generation (Gaussian/Poisson) is insufficient for training restoration models for old films. **Project Degrader** simulates the physics and chemistry of film aging.

### 🌟 Key Features

*   **Physics-Based Simulation:**
    *   **Geometry:** Simulates film creases, warping, and emulsion shifts (chromatic aberration).
    *   **Defects:** "Smart" scratches (Bezier curves/hairs), debris, and dust.
    *   **Chemistry:** Simulates uneven emulsion degradation, chemical stains, and color fading.
*   **Digital Artifacts:** Simulation of Banding (quantization) and MPEG compression.
*   **Advanced GUI:**
    *   **Comparison Viewer:** Real-time preview with a "Magnifier" tool and split-screen.
    *   **Profile Manager:** Save and load complex degradation presets (JSON).
    *   **Batch Processing:** Multi-threaded generation of LQ/GT pairs with probability distribution for different profiles.

### 🖥️ Installation & Running

The application is located in the `Degrader` directory.

**Prerequisites:** Python 3.10+, FFmpeg (for MPEG simulation).

#### Linux / macOS
Use the included launcher to automatically set up the virtual environment:

```bash
cd Degrader
chmod +x launcher.sh
./launcher.sh run
