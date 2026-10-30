Sketch2Style 🎨➡️👗

Turn a rough fashion sketch and a text prompt into a realistic product image in 2–3 seconds.

Sketch2Style is a sketch-to-image fashion design system built on Stable Diffusion v1.5 and ControlNet. Draw a dress or a sneaker, describe it in words, and the model generates a realistic product image that follows your sketch's outline.

<!-- TODO: replace with a GIF or screenshot of the Gradio app -->

Sketch2Style demo

✨ Highlights
	
Model	Stable Diffusion v1.5 + ControlNet (Canny edge conditioning)
Trainable parameters	1.43B, with all four components fully fine-tuned
Training	~14,400 steps, separate learning rate per component
Final training loss	0.0132
SSIM	-0.0024 → 0.22
CLIP text-image alignment	0.185 → 0.202
Inference	2–3 seconds per design on an NVIDIA RTX PRO 6000
🧩 How it works
Sketch
Canny edge map
Text prompt
Text encoder
ControlNet
U-Net denoiser
VAE decoder
Generated product image
Sketch → edge map. The input sketch is converted to a Canny edge map, which tells the model where the garment's outlines are.
Prompt → text embedding. The text prompt describes what the garment should look like (colour, material, style).
ControlNet injects the edge structure into the U-Net at every denoising step, so the output follows the sketch.
U-Net denoises in latent space, guided by both the text and the edges.
VAE decoder turns the final latent into a full image.
📊 Data

We used Fashion-MNIST, restricted to the dress and sneaker classes.

Step	Detail
Training images	9,600
Upscaling	28×28 grayscale → 256×256 RGB
Sketch input	Canny edge maps, thresholds 100 and 200

Known limitation: Fashion-MNIST images are only 28×28. Upscaling makes them the right size but can't add detail that was never there, which shows up as soft textures in the outputs. This is the main thing the next version addresses (see Roadmap).

🏋️ Training

All four components were fully fine-tuned, not frozen or adapted with LoRA:

Component	Role	Learning rate
ControlNet	Sketch conditioning	<!-- TODO -->
U-Net	Denoising	<!-- TODO -->
Text encoder	Prompt understanding	<!-- TODO -->
VAE	Latent encode/decode	<!-- TODO -->
Steps: ~14,400
Final loss: 0.0132
Hardware: <!-- TODO: GPU model, local / university / cloud provider -->
Training time: <!-- TODO -->
Robustness

We tested the model on noisy and blurred sketches, not just clean edge maps, to check it holds up on the kind of messy input a real person draws.

💻 CPU vs GPU vs Cloud: what training at 1.43B parameters taught us
Option	Good for	Problem at this scale
CPU	Data prep, edge map generation	Training would take weeks, not hours
Local consumer GPU	Quick experiments, inference	Not enough memory for full fine-tuning
Cloud / datacenter GPU	Full fine-tuning	Cost per hour, session limits, data transfer

Why memory is the bottleneck: with AdamW in full fp32, every trainable parameter needs about 16 bytes (weights + gradients + two optimizer states). For 1.43B parameters, that's roughly 23 GB before activations, more than most consumer GPUs have. Mixed precision, gradient checkpointing, or 8-bit optimizers reduce this.

<!-- TODO: add what you actually used and any real numbers (cost, hours, OOM errors you hit and how you fixed them) -->
🚀 Quick start

Note: Library versions are pinned in requirements.txt on purpose. Diffusers, Transformers, and PyTorch version mismatches broke our environment more than once, so please don't upgrade them unless you're ready to debug.

bash
git clone https://github.com/Kartavya1905/Sketch2Style.git   # TODO: confirm repo name
cd Sketch2Style
pip install -r requirements.txt
Run the demo
bash
python app.py   # TODO: confirm entry-point file name

Then open the local Gradio URL shown in your terminal.

Model weights
<!-- TODO: link to weights (Hugging Face Hub, Google Drive, etc.) and how to load them -->
📁 Repository structure
<!-- TODO: update to match the actual repo -->
Sketch2Style/
├── app.py              # Gradio web app
├── train.py            # Fine-tuning script
├── data/               # Data preparation and edge-map generation
├── evaluate.py         # SSIM and CLIP evaluation
├── requirements.txt    # Pinned dependencies
└── assets/             # Demo images and GIFs
⚠️ Limitations
Trained only on dresses and sneakers from low-resolution source images.
Textures and fine fabric detail are limited by the 28×28 source data.
The CLIP alignment improvement is modest (0.185 → 0.202); prompt adherence still has room to grow.
🔭 Roadmap

We're working on the next version, aiming for more natural, realistic designs.

<!-- TODO: list concrete next steps only once they're decided, e.g. higher-resolution dataset, more garment classes -->
👥 Team
Kartavya Mandora — GitHub · LinkedIn
Rupa Shetye — <!-- TODO: add her GitHub / LinkedIn -->
🙏 Acknowledgements
Stable Diffusion v1.5
ControlNet by Lvmin Zhang et al.
Hugging Face Diffusers
Fashion-MNIST by Zalando Research
📄 License
<!-- TODO: choose a license. Note that Stable Diffusion v1.5 weights are under the CreativeML Open RAIL-M license, which applies to any fine-tuned weights you distribute. -->
