# deGlare-dctl

Pure per-pixel **Digital Polarizer (CPL)** DCTLs and 3D `.cube` LUTs for **DaVinci Resolve** designed to remove or tame harsh specular glare and flashy softbox reflections on glossy product bottles and shrink-sleeve labels — with zero tracking mesh, zero warp, and zero temporal flicker.

Preserves 100% of 3D parallax, bottle rotation, shadows, water droplets, and foreground occlusions.

---

## Repository Contents

### 1. DCTLs & 3D LUTs (`deglare_kit/`)
| File | Type | Description |
| :--- | :---: | :--- |
| `deglare_kit/Universal_LabelCPL.dctl` | DCTL | **Cross-color Linear-Light Universal Label CPL.** Works on any chromatic label color (Red, Orange, Yellow, Lime, Emerald, Cyan, Blue, Purple, Pink). Supports Rec.709 and Scene-Linear CST (10-bit Log/RAW) pipelines, protects white/silver/black typography, supports single-hue isolation (`Target Hue Only`), illuminant warmth/tint adjustment, and diagnostic view modes. |
| `deglare_kit/MoguMogu_LabelCPL.dctl` | DCTL | **3-Zone Mogu Mogu Mango Label CPL.** Calibrated specifically for Mogu Mogu Mango's lime band, mango graphic, and warm-cream upper label under cool overhead softbox glare. |
| `deglare_kit/MoguMogu_LabelCPL.cube` | 3D LUT | Static `65x65x65` 3D LUT snapshot of `MoguMogu_LabelCPL.dctl` at default sliders (compatible with DaVinci Resolve Free and non-DCTL hosts). |
| `deglare_kit/MoguMogu_DigitalCPL.dctl` | DCTL | **PET Bottle Body CPL.** Calibrated for transparent orange/mango PET bottle bodies using Blue-channel excess separation (`S = max(0, B - beta * G)`). |
| `deglare_kit/MoguMogu_BodyCPL.cube` | 3D LUT | Static `65x65x65` 3D LUT snapshot of `MoguMogu_DigitalCPL.dctl` at default sliders. |

### 2. Reference Frames & Previews
| File | Description |
| :--- | :--- |
| `deglare_kit/donor_label_f200.png` | Reference frame 200 extracted from source footage for calibration. |
| `deglare_kit/donor_label_f215.png` | Reference frame 215 extracted from source footage for calibration. |
| `deglare_kit/MoguMogu_BodyDeGlare_Preview.mp4` | Full-frame preview with bottle body de-glared. |
| `deglare_kit/MoguMogu_BeforeAfter_SideBySide.mp4` | Side-by-side comparison of original vs body de-glare. |
| `deglare_kit/MoguMogu_FullDeGlare_BodyAndLabel_Preview.mp4` | Full-frame preview combining body + label de-glare. |
| `deglare_kit/MoguMogu_FullDeGlare_BeforeAfter.mp4` | Side-by-side comparison of original vs full (body + label) de-glare. |
| `(NO STOP-MO) 26100052 - ^Vania Mogu Mogu WA7051.mp4` | Original source commercial footage (`1280x1920`, `29.97fps`, Rec.709). |
| `asdasd.png` | Reference still frame showing label glare details. |
| `Screenshot 2026-11-25 at 10.34.26 (1).png` | Client feedback reference screenshot. |

---

## Installation (DaVinci Resolve Studio)

1. Copy the desired `.dctl` file(s) (e.g. `deglare_kit/Universal_LabelCPL.dctl`) into DaVinci Resolve's LUT folder:
   - **Windows:** `C:\ProgramData\Blackmagic Design\DaVinci Resolve\Support\LUT\`
   - **macOS:** `/Library/Application Support/Blackmagic Design/DaVinci Resolve/LUT/`
2. Restart DaVinci Resolve (or open **Project Settings → Color Management → Update Lists** / click **Reload DCTL** in the Inspector).
3. In the **Color** page, add a Serial Node, draw a rough **Power Window** around the label area, and apply the DCTL via **OpenFX → Resolve FX Color → DCTL**.

> **Note:** DaVinci Resolve **Free** does not run `.dctl` files without a watermark; use the `.cube` 3D LUTs (`MoguMogu_LabelCPL.cube` / `MoguMogu_BodyCPL.cube`) via the standard LUT menu if using Resolve Free on Mogu Mogu footage.

---

## Quick-Start Guide: `Universal_LabelCPL.dctl`

### Pipeline Setup
- **8-bit / Rec.709 footage:** Apply `Universal_LabelCPL.dctl` directly and keep `Input Space` set to `Rec709 Standard` (or `Rec709 Gamma 24`).
- **10-bit Log / RAW footage (recommended for heavy glare):** Place the DCTL in a node **between** `CST (Log → Linear)` and `CST (Linear → Rec.709)`, and set `Input Space` to `Scene Linear`. Unclipped super-white headroom above `1.0` is preserved without clamping.

### Slider Reference
| Control | Purpose |
| :--- | :--- |
| `Input Space` | `Rec709 Standard` (γ 2.2), `Rec709 Gamma 24`, or `Scene Linear` (for Log/RAW CST workflows). |
| `Target Mode` | `All Chromatic Colors` (automatic across all hues), `Target Hue Only` (isolate one hue), or `Force Blue/Green/Red Dark` (uniform single-color surfaces). |
| `Strength` | Specular glare removal strength (`0.90` default keeps ~10% natural plastic sheen). |
| `Clean Base Floor` | Minimum dark-channel ratio for clean ink (`0.20` default). Raise to `0.35 – 0.55` for **pastel labels** so clean pastel tones are not over-darkened. |
| `White Text Protect` | Saturation gate protecting pure white typography, silver, cream-white, and black ink (`0.25` default). |
| `Highlight Clip Protect` | Attenuates subtraction near `[0.95–1.0]` in Rec.709 to prevent hue artifacts on clipped highlights. |
| `Glare Brightness Floor` | Optional minimum brightness threshold so darker clean areas are untouched. |
| `Glare Light Warmth` / `Tint` | Compensates for warm tungsten (`+`) or cool/tinted LED (`-`) studio reflections. |
| `Target Hue Angle` / `Width` / `Softness` | Used when `Target Mode = Target Hue Only` (`0°` Red, `30°` Orange, `60°` Yellow, `120°` Green, `180°` Cyan, `240°` Blue, `300°` Magenta). |
| `Neutral Flash Rolloff` | Optional highlight knee compression for neutral white streaks (`0.00` off by default). |
| `View Mode` | `Final Output`, `Glare Matte` (grayscale extracted glare), or `Protection Mask` (Green = processed, Blue = protected, Red = near-clip). |

### Recommended Presets
- **Multi-color glossy label (Rec.709):** Defaults (`Rec709 Standard`, `All Chromatic Colors`, `Strength 0.85–0.95`).
- **Pastel label:** `Clean Base Floor = 0.40`, `White Text Protect = 0.30`.
- **Single-hue isolation (e.g. Red logo only):** `Target Mode = Target Hue Only`, `Target Hue Angle = 0`, `Target Hue Width = 35–45`.
- **Warm tungsten studio flash (3200K):** Increase `Glare Light Warmth` (`+0.25` to `+0.45`).

---

## Physical Limitations
No pure per-pixel color-channel DCTL can solve these cases alone:
1. **Pure neutral labels (white, gray, black):** Specular flash and ink share identical R/G/B ratios.
2. **Metallic, chrome, foil, or holographic surfaces:** Conductor reflections tint the glare itself.
3. **3-channel hard-clipped `[255, 255, 255]` whiteout:** Once all three channels clip to pure white, zero underlying print detail remains; multi-frame temporal blending (planar track + `Darken` blend) is required.
