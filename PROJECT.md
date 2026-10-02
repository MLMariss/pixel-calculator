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
  On top: grey = work that doesn't shrink, purple = the upscaler's own run cost.

## Decisions
- GPU list is 3 buckets — **Top / Mid / Budget** — with one NVIDIA and one AMD card each,
  only benchmarked cards (no estimated placements):
  RTX 5090 / RX 9070 XT, RTX 5070 / RX 9070, RTX 5060 / RX 9060 XT.
- VRAM sizes are left out of card names; they play no role in the model.
- Upscaling ratios come from NVIDIA's DLSS Programming Guide (Mar 2026, §3.7.1), per side:
  DLAA 1.0, Quality 1.5, Balanced 1.724, Performance 2.0, Ultra Performance 3.0
  (pixel share 100 / 44.4 / 33.6 / 25 / 11.1 %). Render size = round(native ÷ ratio) per axis.
- Labelled "DLSS / FSR": FSR uses near-identical ratios, so one selector serves both brands.
- **Worst case always** (user decision: don't sell dreams). When upscaling is on, each bar is
  shrunk pixel work + fixed work + upscaler cost:
  - Pixel cut: pure ratio math above.
  - Upscaler cost: heaviest preset L for every mode (no-ray column); ray traced and path traced
    columns pay Ray Reconstruction instead. RTX 5070 measured times (costliest of our cards in units):
    SR-L 1.17/1.87/3.87 ms, RR 1.59/2.72/5.84 ms at FHD/2K/4K (DLSS guide §2.4, DLSS-RR guide §2.2).
    Converted to units as ms × 60 fps × 390 (5070 limit). FSR assumed to cost the same.
  - Fixed share that doesn't shrink, per mode (Q / B / P / UP):
    4K .33 / .29 / .26 / .26, 2K .39 / .34 / .30 / .30, FHD .45 / .39 / .35 / .35.
    4K fitted so the no-ray 4K bar (with the purple cost) reproduces ComputerBase's DLSS 4.5
    results: RTX 5070 Ti, 7-game geomean, UHD, native TAA 50.9 fps → Quality 62.7 (+23%),
    Performance 81.4 (+60%). Source: computerbase.de/artikel/grafikkarten/
    dlss-4-5-4-fsr-4-upscaling-ai-benchmarks.95848/seite-2. Balanced interpolated;
    Ultra Perf held at the Performance value (no data, don't extrapolate gains).
    2K / FHD = 4K × 1.17 / × 1.34 (estimate: no matching CB data at those resolutions;
    sanity check vs TechSpot 1080p DLSS Quality +36% in the DLSS 2/3 era).
  - TechPowerUp and TechSpot block this cloud environment's IP; ComputerBase is reachable.
- Frame generation is not modelled.
