# deep-scatter-pet

Learned scatter estimation for preclinical PET, aimed at a journal paper. BIPI lab, part of NSTC grant
114WFAB910936 (PI 陳志成), Year 1 aim 1.4:

> Develop advanced SC [scatter correction] techniques to enhance imaging reliability and accuracy. DL
> based methods such as U-net architectures for scatter estimation, domain adaptation networks to
> address variable imaging conditions, and probabilistic models for robust scatter modeling will be
> employed.

Status: **draft README, 2026-10-19**. Everything under "Decided" was confirmed by the project lead;
everything else is open.

## Goal

A neural network that sees only the measured sinogram (trues + scatter) predicts the scatter sinogram.

- It must **match SSS** (single scatter simulation, the standard physics-based method) in at least some
  respect. Otherwise there is no point.
- Also check **where it breaks**: objects or count levels unlike its training data, where SSS may keep
  working.

The final story depends on the results.

## Decided

- **Method: keep the prototype design**:
  - sinogram in, scatter sinogram out; reconstruction itself is unchanged;
  - the predicted scatter is **added** to the reconstruction's forward model, never subtracted from the
    data;
  - input planes are mirror-merged: the two planes of a ring pair, (a, b) and (b, a), are added. Each
    plane is indexed by its mean axial position and its ring difference;
  - the input is a window of 7 neighbouring planes along the axis at one ring difference, plus a channel
    holding the ring difference (2.5D);
  - each window is divided by its mean brightness, with a minimum scale, and near-empty planes are
    dropped (empty planes made the first training run blow up);
  - a 3-level U-Net with 48 base channels and a non-negative (softplus) output;
  - **independent labels**: the counts are split at random into two halves (Poisson splitting,
    p = 0.5). The input comes from one half, the label from the other half's scatter, so the label's
    noise is not visible in the input;
  - Poisson negative log-likelihood loss;
  - training, validation and test sets are split by simulation run, never by plane.
- **Ablations come after the main results.** If results are far from what we expect, the design is
  revised, drastically if needed.
- **Data:** simulated with MCGPU-PET through the shared toolkit (`pet-sim-recon`), which separates
  trues and scatter. Random shapes (ellipsoids and elliptic cylinders): some runs with inserts inside a
  larger body that also has activity, some with free-floating shapes. Lower count levels come from
  thinning (keeping each count with probability p).
- **Judging:** reconstruct each test case three ways, with identical settings and the same noise
  realization: no correction (worst), the true simulated scatter (best possible), and the method.
  Report how much of the worst-to-best gap the method closes, measured in the reconstructed image.
- **Real data:** the Bruker scanner gives only its own reconstructed images, and its only scatter
  correction is a dual-energy-window switch. On 2026-10-20 we scan a larger phantom with and without
  that correction. Raw-like data will be synthesized by forward-projecting the 3D images. This is
  weaker than real raw data, and the scanner's correction is a reference, not the truth.

## Open questions

1. Classical baselines: SSS only, or SSS plus convolution subtraction (scatter = a blurred, scaled copy
   of the data). A working SSS prototype exists.
2. Where it breaks: which differences from training to test (larger or denser objects, lower counts,
   activity outside the field of view, realistic mouse anatomy) and how far to push each.
3. Wrong attenuation map: SSS needs one, and in PET/MR it comes from MR and is often wrong; the network
   needs none. Maybe later, depending on results.
4. How large scatter is in mouse-sized objects. The scanner operator says the effect is small on
   preclinical scanners. If so, which object sizes make the study matter?
5. How many simulated runs are needed (the prototype trained on 50), and whether to balance the dataset
   by an estimate of each object's scatter fraction, as the old generator could.
6. What exactly to compare on the synthesized real data.

## Previous works (may serve as a prototype)

- [pet-scatter-correction](https://github.com/electronics10/pet-scatter-correction)
