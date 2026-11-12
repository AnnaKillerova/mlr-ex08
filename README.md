# 🍃 mlr-ex08 — Live Leaf Classifier (browser, ONNX)

**Open the app: <https://annakillerova.github.io/mlr-ex08/>**

Point an iPhone (or any phone/laptop) camera at a leaf and the page classifies it
live into **apple / cherry / chestnut / maple**. Everything runs on the device —
the video never leaves your browser.

## What it does

- Streams the **rear camera** (`getUserMedia`, `facingMode: environment`) and
  classifies frames continuously with **onnxruntime-web** (WebAssembly, loaded
  from the jsDelivr CDN — pinned to `1.31.1`).
- Pre-processing follows [`model_info.json`](model_info.json) exactly:
  1. crop the **central square** of each frame,
  2. resize it to **96 × 96** (`input_size`),
  3. scale RGB pixels to **0–1**,
  4. standardize each channel with `mean = [0.485, 0.456, 0.406]`,
     `std = [0.229, 0.224, 0.225]`,
  5. feed an NCHW tensor of shape **1 × 3 × 96 × 96**.
- The model's output tensor is named `logits`, so the page applies **softmax** and
  shows the **probability of every class live** (bar + number; tiny probabilities
  are shown in scientific notation).
- **Upload / take photo** fallback — the same pipeline runs on a still image
  (EXIF orientation of iPhone photos is handled automatically).
- A QR code at the bottom of the page opens this URL on a phone.

## Correctness check

`leaf_model.onnx` + the pipeline above reproduce `check_expected.json` for
`check_image.jpg` (the 96×96 reference image) to a max absolute deviation of
**3.4 × 10⁻¹⁰** per class, verified with Python `onnxruntime`:

| class | expected | our pipeline |
|---|---|---|
| apple | 0.9999831915 | 0.9999831915 |
| cherry | 1.6820184e-05 | 1.6819848e-05 |
| chestnut | 1.0555983e-08 | 1.0556083e-08 |
| maple | 2.3419852e-10 | 2.3420074e-10 |

## Files

| file | role |
|---|---|
| `index.html` | the whole app (plain JavaScript, no build step, no framework) |
| `leaf_model.onnx` | the classifier (input `image` 1×3×96×96 float32, output `logits` 1×4) |
| `model_info.json` | classes / input size / normalization — read by the page at runtime |

## iPhone notes

- Requires **iOS/iPadOS 16.4+** (WASM SIMD in Safari).
- The camera starts only after you tap **Start camera** — iOS requires a user
  gesture for camera access, and the page must be served over HTTPS (GitHub Pages ✓).
- `<video playsinline muted>` keeps the preview inside the page instead of going
  fullscreen; WebAssembly runs single-threaded because GitHub Pages cannot send
  the cross-origin isolation headers that `SharedArrayBuffer` would require.

## Run locally

```bash
python -m http.server 8000
# open http://localhost:8000/  (getUserMedia needs http://localhost or HTTPS)
```

Deployed via **GitHub Pages** from the `main` branch.
