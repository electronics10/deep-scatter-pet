# Phantom scan session, 2026-11-02 (pilot)

Record of the first phantom scan session on the lab's Bruker 7 T PET/MRI. Facts and readings only;
background and methods are in [docs/phantom-experiments.md](../../docs/phantom-experiments.md).
Identical copies are kept in `mr-pet-synergy`, in `deep-scatter-pet`, and with the data; update all of
them together.

- **Recorded by:** Wei-Cheng Chen, with a radiologist colleague. The scanner operator ran the scanner and
  the post-processing.
- **Status:** draft, 2026-11-03. Transcribed from the handwritten sheet and drawings. Items marked
  **TBD** are still open.

## Purpose

- **mr-pet-synergy:** IQ and Derenzo phantoms at three count levels, with MR images.
- **deep-scatter-pet:** two larger phantoms, reconstructed with and without scatter correction.

## Setup

- **Scanner:** Bruker BioSpec 70/18 7 T MRI with PET insert (sequential PET and MR); console software
  ParaVision 360 V3.1.
- **Workflow per group** (console screenshot): PET acquisition → bed move → MR, sequence
  **T2_TurboRARE** (T2-weighted fast spin echo). The screenshot lists experiment numbers PET E1,
  T2_TurboRARE E5, PET E6, T2_TurboRARE E7; which phantom and level each belongs to: **TBD**.
- **Tracer:** F-18 FDG, 26.25 mCi in the delivery at 09:00.
- **Units:** activity readings in μCi; times are local time (UTC+8).
- **Scan start times** were not recorded. Each scan started about 1 minute after the time written for it;
  derived values use recorded time + 1 min.
- **Clock differences** (dose calibrator vs scanner): not recorded.
- **List-mode:** the operator agreed to provide it (**TBD**: received?).

## Phantoms

| Phantom | Contents | Used for |
| --- | --- | --- |
| NEMA NU 4 image-quality (IQ), mouse size | Upper part: FDG solution around two cold chambers, one water, one air. Lower part: 5 hot rods (1–5 mm), FDG solution, connected to the upper part (one FDG compartment, filled from two syringes). Bottom of the lower part: air or solid plastic, **TBD**. | mr-pet-synergy |
| Rat-sized Derenzo | Hot rod groups, FDG solution. | mr-pet-synergy |
| Three-cold-rod | FDG solution; all rods air. | deep-scatter-pet |
| Concentric-circle | Inside out: centre tube air; middle layer FDG solution; outer layer water without FDG. | deep-scatter-pet |

- **Filling:** water first, then FDG. Phantoms were **not completely full**. Only 1 mm needles had been
  brought, and liquid flows through them very slowly, so filling a phantom to the top would have taken
  far longer than the scanner time allowed. Filling was stopped at roughly the same amount for every
  phantom of a kind; the weights are the reference.
- **Weights:** whole filled phantom, including plastic. Empty weights **TBD**.
- **Compartment sizes:** **TBD**.

## Readings (as written on site)

| # | Phantom | Level | Weight | Syringe before | Syringe after | PET | MR |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | IQ | full | 59 g | lower part: 122 μCi at 09:08; upper part: 118 μCi at 09:10 | 21 μCi and 20 μCi at 09:15 | 20 min | 7 min |
| 2 | Derenzo | full | 159 g | 115 μCi at 09:44 | 20 μCi at 09:44 | 20 min | 7 min |
| 3 | Three-cold-rod | full | 55 g | 117 μCi at 10:19 | 17 μCi at 10:19 | 20 min | none |
| 4 | Concentric-circle | full | 194 g | 239 μCi at 10:38 | 20 μCi at 10:38 | 20 min | none |
| 5 | IQ | decay only | 59 g | total 92 μCi at 11:09 (by decay) | — | 10 min | 3 min |
| 6 | Derenzo | decay only | 159 g | total 51 μCi at 11:25 (by decay) | — | 10 min | 3 min |
| 7 | IQ | part withdrawn, refilled with water, mixed | 60 g | total 31 μCi at 11:46 | — | 2 min | 3 min |
| 8 | Derenzo | part withdrawn, refilled with water, mixed | 158 g | total 12 μCi at 11:59 | — | 2 min | 3 min |

The phantoms were taken out and put back between groups (rows 1, 5, 7 and rows 2, 6, 8).

## Derived values

Methods: [docs/phantom-experiments.md](../../docs/phantom-experiments.md), "Truth from dose calibrator
readings" and "Count levels". F-18 half-life 109.8 min.

**Injected activity**

| Phantom | Injected | Check |
| --- | --- | --- |
| IQ | 95.7 + 94.3 = **190.1 μCi (7.03 MBq)** at 09:15 | decays to 92.5 μCi at 11:09; recorded 92 |
| Derenzo | **95 μCi (3.51 MBq)** at 09:44 | decays to 50.2 μCi at 11:25; recorded 51 |
| Three-cold-rod | **100 μCi (3.70 MBq)** at 10:19 | — |
| Concentric-circle | **219 μCi (8.10 MBq)** at 10:38 | — |

**Count levels** (relative to the 20-minute full scan of the same phantom; estimates, to be replaced by
list-mode count totals)

| Row | Phantom | Activity at start (vs full) | Counts (vs full) |
| --- | --- | --- | --- |
| 5 | IQ | 91.4 μCi (48%) | 25.0% |
| 7 | IQ | 30.8 μCi (16%) | 1.7% |
| 6 | Derenzo | 50.7 μCi (54%) | 27.7% |
| 8 | Derenzo | 11.9 μCi (13%) | 1.3% |

**Activity concentration:** **TBD**. Needs empty weights and FDG compartment volumes (IQ: excluding the
water cold chamber; concentric-circle: middle layer only).

## Reconstructions by the scanner software

- **All images:** 3D MLEM, 18 iterations, 1 subset, 0.25 mm voxels, no filter; attenuation correction on,
  applied in post-processing.
- **IQ and Derenzo:** scatter correction by SSS.
- **Three-cold-rod and concentric-circle:** two versions each, with and without SSS (4 datasets). SSS was
  requested explicitly; dual-energy-window correction was also available.
- **Attenuation map used: unknown (TBD).** The three-cold-rod and concentric-circle phantoms had no MR
  scan, so their map may not contain the phantom. That would affect both the attenuation correction
  and the SSS estimate of these 4 datasets.

## Known issues

1. Scan start times not recorded (estimated as recorded time + 1 min).
2. Phantoms not completely full: air along the upper side in the bore.
3. Phantoms moved between count levels: images of different levels need rigid registration. The
   operator registers PET and MR within each group.
4. Attenuation map unknown, possibly missing the phantom for the PET-only scans.

## Data files

**TBD:** storage location; file names of PET DICOM, MR DICOM and list-mode files.

## To do

- [ ] Empty weights; volume of each compartment (IQ: also the water cold chamber).
- [ ] Bottom of the IQ lower part: air or solid plastic (drawings or a senior lab member).
- [ ] Receive list-mode; record file names and storage location.
- [ ] Read DICOM headers: acquisition times, attenuation and scatter correction tags, MR parameters.
- [ ] Ask the operator for (all post-processing, no new scans):
  - [ ] **list-mode files** of all 8 acquisitions, plus whatever is needed to use them: the file format
        description, normalization and calibration files, and the detector geometry (block positions,
        depth-of-interaction layers);
  - [ ] the **exact acquisition start and end times** from the console log;
  - [ ] **reconstructions with attenuation and scatter correction both off**, same settings otherwise
        (MLEM, 18 iterations, 0.25 mm);
  - [ ] for the three-cold-rod and concentric-circle phantoms, also **attenuation on, scatter off**, and
        a **dual-energy-window** version, to compare with SSS;
  - [ ] the **attenuation maps** actually used, exported as images: this shows directly whether the
        phantom is in them;
  - [ ] the **scatter estimates** from SSS, if they can be exported;
  - [ ] **screenshots of the reconstruction settings**, and the ParaVision version;
  - [ ] what the image values mean: units (Bq/mL?), and the time they are decay-corrected to (scan
        start or another reference time);
  - [ ] which other corrections were on: randoms, dead time, decay, normalization;
  - [ ] **all MR DICOM series**, including localizers, and the PET–MR registration within each group;
  - [ ] fallback, only if we cannot read the list-mode ourselves: the full scans re-framed into short
        frames (e.g. 1, 2, 5, 10, 25%, 5 frames each), reconstructed with the same settings.
- [ ] Replace the estimated count levels with list-mode count totals.
