# CyanoLUT

**Step wedge generator and correction curve for cyanotype printing.**

*[Versión en español](README.es.md)*

A single self-contained HTML file. No server, no installation, no data leaves your browser. Open it and it works — including offline, except for the PDF export, which loads jsPDF from a CDN.

Available in Spanish, English, French and Basque (ES / EN / FR / EU).

---

## What it does

Cyanotype does not respond linearly to light. A negative that looks perfectly graded on screen prints with crushed highlights and blocked shadows. The usual fix is a correction curve (LUT) applied to the negative before printing — but that curve has to be *measured*, because it belongs to your particular chain: your printer, your transparency film, your UV source, your sensitizer, your water.

CyanoLUT measures it, in two tabs:

**01 · Generate wedge** — Produces a 21- or 31-step grayscale wedge, linear in gray value, laid out to fill an A4 sheet. Download as PDF (real size, no scaling) or PNG, and print it as a negative onto polyester/acetate.

**02 · Analyze & curve** — Load the scan of the developed, dry print. Mark each column with two clicks (first and last step). The tool measures the process response and builds an inverse correction curve, exportable as `.cube`, `.acv` and `.csv`.

Apply the LUT to your future negatives, in grayscale, before printing them.

---

## Workflow

1. **Find your exposure time first.** CyanoLUT does not calculate it. Use the sliding-opaque-card method over equal intervals, with the clean film on top so its base density is included.
2. **Generate and print the wedge** at real size on polyester.
3. **Expose and develop** exactly as you do in production.
4. **Dry for ≥24 h.** Prussian blue keeps intensifying through oxidation.
5. **Scan with every automatic correction disabled.**
6. **Load the scan**, select the *same* number of steps you generated, adjust the measurement window, and mark the columns left to right.
7. **Review the curve** and export.
8. **Apply the LUT** to a real negative in grayscale, print, expose. The corrected print should distribute its tones far better.

---

## Design decisions worth knowing

**Measure the red channel.** Prussian blue absorbs strongly in the red, so that is where the pigment actually modulates. Measured on the same scan, red gave roughly 49 % more density range than luminosity, about twice the discrimination in the deepest shadows, and a cleaner raw curve (fewer monotonicity reversals). The feared shadow saturation does not materialize at realistic cyanotype Dmax. Once you pick a channel, stay with it — LUTs measured on different channels are not comparable.

**Median, not mean.** Sampling uses the median over a rectangular window proportional to the patch, so dust, development stains and scanner grime do not drag the reading.

**PAVA + PCHIP, no parametric model.** Isotonic regression enforces monotonicity; a Fritsch–Carlson monotone spline turns it into a continuous curve sampled at 256 points. A Weibull saturation model was tried and discarded: it does not capture the S-shape of the process (structural residual up to ~0.167 in density) and its parameters degenerate. When there is no established theoretical curve, assuming only monotonicity is the honest choice.

**Sacrificial band (laser printers).** Optional black bar above the patch block. Laser printers over-deposit toner on the first strip of the sheet entering the fuser — a generic electrophotography phenomenon, amplified on non-absorbent PET. Without the band, the top patch of each column prints lighter than it should and the curve shows a non-monotonic dip. The band takes the hit instead. Not needed on inkjet (no fuser), which is why it is off by default.

**Discarding points is a patch, not a fix.** You can click a point on the response panel to exclude it; it is reconstructed by linear interpolation between healthy neighbours. Use it only when you can name the physical cause (fuser edge, coating irregularity, a drop of water). The difference between "this point is corrupted by X" and "this point breaks the smoothness" is the difference between calibrating and massaging data.

**Plateaus mean overexposure, not underexposure.** If several steps measure *exactly* the same density, you are saturating the most transparent patches. Counterintuitive but measured: reducing the time separated the extremes and *raised* the measured Dmax. Inverting a curve with a flat segment produces a jump in the LUT — mathematically unavoidable, and visible as posterization in the print. Fix it at the exposure stage; smoothing only masks it.

---

## Requirements

A browser. That is all. jsPDF is loaded from a CDN solely for the PDF export; if it fails to load, the PNG export still works offline.

---

## Validation

The full loop has been validated in practice on 21 steps: generate → expose → measure → build LUT → apply to a real negative → reprint, with the response curve visibly approaching the ideal diagonal and shadow detail recovered that had disappeared uncorrected.

Reference calibration conditions: 8′30″ exposure, Avery PET film, 365 nm UV LED on an enlarger column, forced oxidation with peroxide, red channel, unadjusted flatbed scan.

The 31-step mode is implemented and verified on screen but has not yet been validated on paper.

**A LUT is only valid while the chain that produced it stays the same** — printer, toner, film, light source, sensitizer, chemistry. Change any of them and recalibrate.

---

## License

MIT © 2026 Alberto Areta

---

## Also by the same author

[Negativo Lab](https://github.com/alareta/NEGATIVO-LAB) — free browser-based film negative development tool.
