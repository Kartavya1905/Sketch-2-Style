# Sketch2Style 🎨➡️👗

Turn a rough fashion sketch and a text prompt into a realistic product image in 2 to 3 seconds.

Sketch2Style is a sketch-to-image fashion design system built on Stable Diffusion v1.5 and ControlNet. Draw a dress or a sneaker, describe it in words, and the model generates a realistic product image that follows your sketch's outline.

This was a two-person course project for AI688 (Spring 2026) in the MS in Artificial Intelligence program at Long Island University Brooklyn.

## Highlights

| | |
|---|---|
| Model | Stable Diffusion v1.5 + ControlNet (Canny edge conditioning) |
| Trainable parameters | 1.43B, with all four components fully fine-tuned |
| Training | About 14,400 steps, separate learning rate per component |
| Final training loss | 0.0132 |
| SSIM | -0.0024 → 0.22 |
| CLIP text-image alignment | 0.185 → 0.202 |
| Inference | 2 to 3 seconds per design on an NVIDIA RTX PRO 6000 |

## How it works

```
Sketch ──> Canny edge map ──> ControlNet ──┐
                                           ├──> U-Net denoiser ──> VAE decoder ──> Product image
Text prompt ──> Text encoder ──────────────┘
```

1. **Sketch to edge map.** The input sketch is converted to a Canny edge map, which tells the model where the garment's outlines are.
2. **Prompt to text embedding.** The prompt describes what the garment should look like (colour, material, style).
3. **ControlNet** injects the edge structure into the U-Net at every denoising step, so the output follows the sketch.
4. **U-Net** denoises in latent space, guided by both the text and the edges.
5. **VAE decoder** turns the final latent into a full image.

## Data

We used Fashion-MNIST, restricted to the dress and sneaker classes.

| Step | Detail |
|---|---|
| Training images | 9,600 |
| Upscaling | 28×28 grayscale → 256×256 RGB |
| Sketch input | Canny edge maps, thresholds 100 and 200 |

**Known limitation:** Fashion-MNIST images are only 28×28. Upscaling makes them the right size but cannot add detail that was never there, which shows up as soft textures in the outputs.

## Training

All four components were fully fine-tuned, not frozen or adapted with LoRA:

| Component | Role |
|---|---|
| ControlNet | Sketch conditioning |
| U-Net | Denoising |
| Text encoder | Prompt understanding |
| VAE | Latent encode and decode |

We also tested the model on noisy and blurred sketches, not just clean edge maps, to check that it holds up on the kind of messy input a real person draws.

## What training at 1.43B parameters taught us

| Option | Good for | Problem at this scale |
|---|---|---|
| CPU | Data prep, edge map generation | Training would take weeks, not hours |
| Local consumer GPU | Quick experiments, inference | Not enough memory for full fine-tuning |
| Cloud or datacenter GPU | Full fine-tuning | Cost per hour, session limits, data transfer |

**Why memory is the bottleneck:** with AdamW in full fp32, every trainable parameter needs about 16 bytes (weights, gradients, and two optimizer states). For 1.43B parameters that is roughly 23 GB before activations, more than most consumer GPUs have. Mixed precision, gradient checkpointing, or 8-bit optimizers reduce this.

## Running it

The full pipeline (data preparation, edge map generation, training, evaluation, and the Gradio demo) is in one notebook:

1. Open `Sketch2Style_GC_Optimized_latest_Updated_Final_Midterm.ipynb` in Jupyter or Google Colab.
2. Use a GPU runtime. Full fine-tuning needs a large-memory GPU (see the memory note above).
3. Run the cells in order. The last section launches the Gradio app and prints a local URL.

**Note on versions:** Diffusers, Transformers, and PyTorch version mismatches broke our environment more than once. If the notebook fails on import, check the library versions first.

## Repository contents

```
Sketch-2-Style/
├── Sketch2Style_GC_Optimized_latest_Updated_Final_Midterm.ipynb   # Full pipeline and Gradio demo
├── Final Submission/                                                # Final course submission files
├── Kartavya Mandora_Group 3_ AI688-Spring26_Midterm.pdf            # Midterm report
├── Kartavya Mandora_Group 3_ AI688-Spring26_Midterm.docx           # Midterm report (Word)
├── Kartavya Mandora_Group 3_ AI688-Spring26_PPT_Midterm.pdf        # Midterm slides
├── Kartavya Mandora_Group 3_ AI688-Spring26_PPT_Midterm.pptx       # Midterm slides (PowerPoint)
└── README.md
```

## Limitations

- Trained only on dresses and sneakers from low-resolution source images.
- Texture and fine fabric detail are limited by the 28×28 source data.
- The CLIP alignment gain is modest (0.185 → 0.202), so prompt adherence still has room to grow.

## Next steps

Training on higher-resolution fashion images to get more natural, realistic textures.

## Team

- **Kartavya Mandora** ([GitHub](https://github.com/Kartavya1905) · [LinkedIn](https://linkedin.com/in/kartavya648)): data preparation, Canny edge pipeline, Gradio app, documentation
- **Rupa Shetye**

## Acknowledgements

- Stable Diffusion v1.5
- ControlNet by Lvmin Zhang et al.
- Hugging Face Diffusers
- Fashion-MNIST by Zalando Research
