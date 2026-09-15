# AI Photo Editing with Inpainting

A web app that swaps the background of a photo — or the subject itself — for an image
generated from a text prompt. Click the subject, SAM segments it, Stable Diffusion XL
inpaints the rest.

![The app in action](AppGenImage.png)

## How it works

| Stage | Model | What it does |
|---|---|---|
| Segmentation | `facebook/sam-vit-base` | Turns one or more clicked points into a binary subject mask |
| Inpainting | `diffusers/stable-diffusion-xl-1.0-inpainting-0.1` | Regenerates the masked region from a prompt |

The mask can be inverted, so the same pipeline either keeps the subject and replaces
the background, or keeps the background and replaces the subject. Clicking additional
points refines the mask interactively before anything is generated.

## Getting started

Requires a CUDA GPU — both models are loaded with `.to("cuda")` and SDXL inpainting is
not practical on CPU.

```bash
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook inpainting.ipynb
```

Run the notebook top to bottom. The final cells launch the Gradio app and print a
public share URL — open that rather than the inline preview. `my_app.close()` shuts it
down.

To launch the app directly instead, import `generate_app` from [`app.py`](app.py) and
pass it your `get_processed_inputs` and `inpaint` functions.

## Repository layout

```
inpainting.ipynb   Notebook: model setup, segmentation, inpainting, app launch
app.py             Gradio interface (segment → prompt → inpaint)
car.png            Example images, preloaded into the app
dragon.jpeg
monalisa.png
AppGenImage.png    Screenshot used in this README
```

## Parameters worth tuning

- **CFG scale** (default 7) — how strictly the generation follows the prompt.
- **Negative prompt** — `"artifacts, low quality, distortion"` works well as a default.
- **Seed** (default 74294536) — fixed so results are reproducible; change it to
  resample without touching the prompt.

## Attribution

Built as a project for the Udacity Generative AI Nanodegree. The Gradio scaffold in
`app.py` and the example images are Udacity course material — see
[LICENSE.txt](LICENSE.txt).
