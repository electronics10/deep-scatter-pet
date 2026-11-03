# Phantom experiments on the Bruker 7 T PET/MRI: a technical guide

How to plan, run, record and analyse phantom scans on the lab's scanner, with the background needed to
understand the choices. Session records (in `experiments/`) refer to this guide instead of repeating it.
Identical copies are kept in `mr-pet-synergy` and `deep-scatter-pet`; update both together.

Status: draft, 2026-11-03.

## 1. Counts and noise

- PET counts single decays, and decays are random. If a region should give N counts on average, a scan
  gives about N ± √N. Relative noise is therefore about 1/√N: 10,000 counts → 1%, 100 counts → 10%.
- **Noise level** = how grainy an image is. Two common measures:
  - standard deviation in a uniform region divided by its mean (needs one image);
  - **ensemble noise**: how much each voxel varies across repeated, independent frames at the same count
    level (needs several frames, at least 5).
- Fewer counts means more noise. Low-dose PET is a problem of few counts.

## 2. Count levels

- **Counts ≈ activity × scan time × scanner sensitivity.** 1% of the activity for 10 min gives about the
  same counts, and so the same noise, as full activity for 6 s.
- The two are not identical: at high activity there are more random coincidences, and the detector is
  busier (dead time). Usually a small effect; check it once (see the last bullet).
- **Ways to make count levels, best first:**
  1. **Re-frame list-mode data:** one long acquisition, cut into frames of any length afterwards. The
     object stays identical at every level, and many repeated frames come for free.
  2. **Frames defined before the scan** (dynamic acquisition).
  3. **Separate short scans,** one after another.
  4. **Lower activity:** wait for decay, or withdraw part of the solution and refill with water (mix
     well). Closest to a real low-dose scan, but the phantom usually moves, and every level needs its own
     activity value.
- **Levels need to be known, not exact.** 1.3% is fine if we know it is 1.3%. List-mode count totals
  give the ratio directly.
- **Check that short frames behave like low activity:** compare a real low-activity scan with a frame
  cut from a full-activity scan that has the same number of counts.
- Very low levels (around 1%) may show almost nothing; include intermediate levels (5–25%).

## 3. Attenuation and attenuation correction

- Both photons of a decay must leave the object. Together they travel the whole line of response (LOR)
  through the object, so the chance that both survive is

  **a = exp(−∫ μ dl)** along the whole LOR,

  independent of where on the line the decay happened. μ is the linear attenuation coefficient at
  511 keV.
- So attenuation is one factor per sinogram bin. In the forward model **ȳ = a · (G x) + s**, where G is
  the geometric system matrix and s the scatter: each row of the system matrix is multiplied by its own a.
- **Size of the effect** (water, μ ≈ 0.096 /cm): a line through the centre of a 3 cm (mouse-sized) object
  keeps about 75% of its counts; through a 6 cm (rat-sized) object, about 56%. Without correction, images
  are darker in the centre and absolute values are too low.
- **The hard part is the μ-map** (μ at every position):
  - **from CT:** CT numbers converted to μ at 511 keV; the most accurate;
  - **from MR:** MR cannot see bone or plastic, so the image is split into a few tissue classes, each
    given one μ;
  - **from known geometry:** for phantoms, draw the μ-map from the drawings (water 0.096 /cm, PMMA about
    0.11 /cm); usually the most accurate for phantoms.
- **SSS also needs the μ-map** (where photons scatter). A wrong μ-map spoils both corrections.
- With list-mode data we can reconstruct ourselves with a known μ-map. So always also ask for the
  scanner's reconstructions **without** attenuation and scatter correction: the only version whose
  contents are fully known.

## 4. Scatter correction on this scanner

- The scanner software offers **single scatter simulation (SSS)** and **dual-energy-window** correction,
  applied in post-processing.
- Measured scatter fractions (NEMA scatter phantoms, Doss et al. 2022): **6.9% mouse-sized, 14.2%
  rat-sized**. Scatter matters more in larger objects.

## 5. The scanner

- **System:** Bruker BioSpec 70/18 USR 7 T MRI with a PET insert. PET and MR run one after the other.
- **PET detector:** 3 rings of 8 monolithic (continuous) LYSO blocks, 50 × 50 × 10 mm, read by SiPMs,
  with 10 depth-of-interaction layers. Ring inner diameter 105 mm; axial field of view 150 mm,
  transaxial 80 mm.
- **Acquisition:** energy window 358–664 keV; coincidence window 12 ns (Doss et al.); sensitivity about
  7–8%. Data are recorded in list-mode.
- **Reconstruction:** 3D MLEM on GPU; voxel sizes 0.25, 0.5 or 0.75 mm. Corrections (attenuation,
  scatter, randoms, decay, dead time) can be applied in post-processing.
- **Console software:** ParaVision 360 V3.1 (as of 2026-11). PET and MR are run as one workflow: PET
  acquisition, bed move, MR sequence (e.g. T2_TurboRARE).
- **Attenuation map:** Bruker states that ParaVision 360 computes it automatically from MR, and adds the
  animal cradle and RF coil. The segmentation and coefficients are not public. On the same system model,
  Rida et al. 2026 used pre-calculated CT-based maps of the cradle and coil. Bruker's update notes list
  attenuation datasets for PET-optimized RF coils only from version 3.2, newer than ours, so our maps may
  not include the coil (to check). What happens without an MR scan is unknown: check the ParaVision 360
  manual on the console.
- **DICOM tags worth reading** in PET images: attenuation correction method (0054,1101), scatter
  correction method (0054,1105), corrections applied (0028,0051), acquisition and series times. MR
  images carry their sequence parameters (repetition time, echo time, slice thickness, resolution).

## 6. Phantoms in the lab

| Phantom | Measures | Notes |
| --- | --- | --- |
| NEMA NU 4 image-quality (IQ), mouse size | Uniformity (uniform region); recovery coefficient of 5 hot rods, 1–5 mm (partial volume); spill-over ratio in a water and an air cold chamber (scatter and spill-over) | Standard phantom, results comparable with the literature. Hot rods connect to the uniform region (one compartment). |
| Derenzo (rat size) | Resolution: the smallest rod group that can still be separated | Rods are clearly visible in MR. |
| Three-cold-rod | Activity seen inside cold rods (mostly scatter error) | Direct test of scatter correction. |
| Concentric-circle | Scatter from a non-emitting outer water layer | Air centre, FDG middle layer, water outer layer. |
| Uniform cylinder | Noise, uniformity, calibration | Larger size means more scatter. |

**MR visibility, useful for MR-guided methods:** a water-filled cold chamber is cold in PET but looks
almost like the surrounding FDG solution in MR (only its wall shows): a PET edge without an MR edge. An
air chamber is dark in both.

## 7. Truth from dose calibrator readings

- **Injected activity** = syringe before (decayed to the time of the "after" reading) − syringe after.
  Decay: A(t) = A(t₀) · 2^(−(t − t₀)/T½), with T½ = 109.8 min for F-18.
- **Activity concentration (the truth)** = injected activity ÷ volume of the FDG solution, decay-corrected
  to each frame. Subtract compartments filled with plain water.
- **Volume:** (filled weight − empty weight), with 1 g ≈ 1 mL for water.
- **Count level of a scan:** activity at scan start × ∫ decay over the scan time, compared between scans
  of the same phantom.

## 8. Session checklist

**Before**
- Weigh every empty phantom; know each compartment's volume.
- Bring needles large enough to fill phantoms completely. With 1 mm needles, filling is so slow that the
  phantoms of the 2026-11-02 session could not be filled to the top. Fill with water first, add FDG, top
  up, mix, remove bubbles.
- Ask the operator: list-mode available? Corrections available? How is the attenuation map made?

**During**
- Note the clock difference between the dose calibrator and the scanner.
- For each phantom: syringe readings before and after with times; weight; **scan start time** of every
  acquisition; frame lengths; MR sequences and durations; a photo of the position.
- Scan MR for every phantom if the attenuation map depends on it.

**After: what to ask the operator for** (post-processing only)
- **List-mode files**, with what is needed to use them: file format description, normalization and
  calibration files, detector geometry (block positions, depth-of-interaction layers).
- **Exact acquisition start and end times** from the console log.
- **Reconstructions with several correction settings**, all other settings identical:
  - attenuation off, scatter off (the only fully known version);
  - attenuation on, scatter off;
  - attenuation on, scatter on (SSS; for scatter studies also dual-energy-window).
- **The attenuation maps actually used**, exported as images.
- **The scatter estimates**, if they can be exported.
- **Screenshots of the reconstruction settings**, and the ParaVision version.
- **What the image values mean:** units (e.g. Bq/mL) and the reference time of the decay correction.
- **Which other corrections were on:** randoms, dead time, decay, normalization.
- **All MR DICOM series** (including localizers), and the PET–MR registration.
- **Fallback, if list-mode cannot be read:** re-framed short frames from the long scans, reconstructed
  with the same settings.

**After: our side**
- Collect list-mode and DICOM files in the data storage; write the session record the same week.

## References

- Doss K.K.M. et al., "Performance Evaluation of a PET of 7T Bruker Micro-PET/MR Based on NEMA NU 4-2008
  Standards", *Electronics* 2022, 11, 2194. https://doi.org/10.3390/electronics11142194
- Rida H. et al., "Evaluation of precalculated attenuation correction map for preclinical cardiac PET/MR
  using a 1H/23Na surface coil", *EJNMMI Physics* 2026, 13:17.
  https://pmc.ncbi.nlm.nih.gov/articles/PMC12901769/
- Bruker, ParaVision 360 PET/MR.
  https://www.bruker.com/en/products-and-solutions/preclinical-imaging/paravision-360/paravision-360-pet-mr.html
