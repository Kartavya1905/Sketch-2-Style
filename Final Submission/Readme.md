📌 Sketch2Style: Controllable Fashion Generation
🚀 Overview

Sketch2Style is a diffusion-based AI system that generates fashion images from sketch + text input.
It combines Stable Diffusion v1.5 + ControlNet to enforce spatial structure (sketch) and semantic style (text prompt).

The model is trained on Fashion-MNIST (Dress & Sneaker categories) and demonstrates strong structure preservation with controllable generation.

📂 Dataset

We use the Fashion-MNIST dataset (filtered to 2 categories):

👗 Dress (label 3)
👟 Sneaker (label 7)

🔗 Dataset link:
https://github.com/zalandoresearch/fashion-mnist

Total used: ~9,600 training samples
Images: 28×28 grayscale → resized to 256×256 RGB
Sketches generated using Canny Edge Detection
⚙️ Model Architecture
Stable Diffusion v1.5 (base model)
ControlNet (Canny conditioning)
CLIP Text Encoder (prompt understanding)
VAE (image encoding/decoding)

✅ Final implementation uses full model fine-tuning (100% trainable) with different learning rates.

🧠 How the Model Works
Input image → converted to edge map (sketch)
Text prompt → encoded using CLIP
Noise added in latent space
ControlNet enforces sketch structure
Stable Diffusion generates final image
🏃‍♂️ How to Run
1. Install dependencies
pip install torch torchvision diffusers transformers accelerate opencv-python gradio
2. Run the notebook
Open the .ipynb file in Google Colab (recommended with GPU)
Run all cells sequentially
3. Generate output
Provide:
Sketch (edge image or uploaded sketch)
Prompt (e.g., “a sneaker, fashion product photo, studio lighting”)
Model outputs generated fashion image
🎯 Results (Final Model)
Metric	Value
SSIM	0.2217
CLIP Score	0.2021
FID	253.21
Inference Time	~2–3 sec

👉 Model prioritizes structure control over photorealism

📊 Key Features
✔ Sketch-conditioned image generation
✔ Strong structural alignment (ControlNet)
✔ Robust to noisy/messy sketches
✔ Interactive Gradio interface
⚠️ Limitations
Low-resolution dataset → blurry outputs
Limited categories (Dress & Sneaker)
High FID due to dataset mismatch (grayscale vs RGB)
🔮 Future Work
Higher resolution (512×512)
Use DeepFashion dataset
Improve CLIP (>0.30) and FID (<100)
Multi-category training
📌 Summary

Sketch2Style demonstrates that ControlNet-based diffusion models can successfully enforce spatial control from sketches, making it useful for AI-assisted fashion design and rapid prototyping.
