# Pixel calculator — project notes

Single-page 3D chart (`index.html`, three.js r128 from cdnjs) showing how much
GPU work per second a game needs at 60 fps, against the limit of a chosen card.

## What is what
- **Bars**: work per second = rendered pixels × 60 fps × cost per pixel.
  Grid is resolution (FHD / 2K / 4K) × rendering mode (no rays / ray traced / path traced).
- **Bar segments**: normal drawing (1), one ray per pixel (1), bounced rays (3, path tracing only).
- **1 unit** = work of fully drawing one pixel the normal way on a current NVIDIA card.
- **Limit plane**: what the selected card can handle (`cap`, million units/s),
  set from Witcher 3 Remastered results without rays (mainly TechSpot, Sept 2026).
- **Upscaling (DLSS / FSR)**: shrinks bars to the pixels actually rendered;
  dashed outline shows the native-resolution bar.

## Decisions
- GPU list is 3 buckets — **Top / Mid / Budget** — with one NVIDIA and one AMD card each,
  only benchmarked cards (no estimated placements):
  RTX 5090 / RX 9070 XT, RTX 5070 / RX 9070, RTX 5060 / RX 9060 XT.
- VRAM sizes are left out of card names; they play no role in the model.
- Upscaling ratios come from NVIDIA's DLSS Programming Guide (Mar 2026, §3.7.1), per side:
  DLAA 1.0, Quality 1.5, Balanced 1.724, Performance 2.0, Ultra Performance 3.0
  (pixel share 100 / 44.4 / 33.6 / 25 / 11.1 %). Render size = round(native ÷ ratio) per axis.
- Labelled "DLSS / FSR": FSR uses near-identical ratios, so one selector serves both brands.
- Upscaler run cost (~0.5–2 ms/frame) is **not** modelled — pure pixel math; noted in the footer.
- Frame generation is not modelled.
