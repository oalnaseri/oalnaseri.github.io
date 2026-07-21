---
layout: page
title: QCommE2E — End-to-End Quantum Communication Simulator
description: An open-source Python framework for simulating end-to-end quantum communication systems — quantum channels, transceiver blocks, quantum autoencoders, and a matched-detection channel benchmark (16-QAM + pretty-good measurement).
img: assets/img/qcomme2e/pipeline.png
importance: 2
category: work
related_publications: true
github: https://github.com/oalnaseri/QCommE2E
img: assets/img/qcomme2e/cover.png
---

**QCommE2E** is my open-source simulation framework for **end-to-end (E2E) quantum
communication systems**. It brings physically distinct impairments — qubit-level
noise, bosonic optical loss, atmospheric turbulence, and polarization-mode
dispersion — into a **single, mathematically interpretable, executable pipeline**,
so that transmitters, channels, receivers, metrics, and learned components can be
assembled, inspected, and extended in one place.

<div class="row justify-content-center">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/pipeline.png" title="QCommE2E end-to-end pipeline" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    End-to-end flow: classical bits → 16-QAM embedding → quantum state preparation →
    quantum channel → pretty-good measurement (PGM) → decision → BER/SER. Every channel
    exposes the same density-matrix interface, so heterogeneous impairments run inside
    one loop, with a constellation / Bloch-sphere visualization layer for inspection.
</div>

---

## Why this project

End-to-end communication modeling increasingly mixes signal-processing abstractions,
probabilistic channel models, and learned transmitter/receiver components. In the
**quantum** setting this is harder, because different impairments naturally live in
different mathematical state spaces: qubit depolarization is a completely
positive trace-preserving (CPTP) map on a 2-dimensional Hilbert space, while optical
attenuation and turbulence are more naturally written in **bosonic** or
**wave-propagation** form.

QCommE2E resolves this with two design goals held simultaneously:

- **Mathematical interpretability** — each stage maps to an explicit equation.
- **Executability** — heterogeneous channels run inside a _common_ simulation loop
  through a shared density-matrix ($$\rho$$) interface.

The result is a reusable environment rather than a one-off benchmark script — spanning
channel models, transceiver abstractions, metrics, visualization utilities, E2E
training hooks, equalization modules, and tutorial notebooks.

---

## Features

- **Quantum channels** — Bosonic Gaussian (loss/thermal), depolarizing, dephasing,
  erasure, PMD, FSO turbulence (Málaga / Kolmogorov), and composite cascades.
- **Transceiver blocks** — state preparation (coherent, squeezed, OAM), modulation,
  POVM measurement, homodyne detection, and photon counting.
- **Quantum autoencoders (QAE)** — spatial-mode QAE, data re-uploading QAE, and
  variational QAE architectures for learned channel compensation.
- **Equalization** — a quaternion-based Kalman adaptive equalizer and neural
  (GNN / deep-unfolding) equalizers.
- **Metrics** — BER/SER, mutual information (MI), generalized mutual information
  (GMI), quantum fidelity, and entanglement measures.
- **E2E training** — joint optimization of transmitter and receiver parameters via
  gradient descent.

---

## Primary use case — the quantum-channel baseline benchmark

The featured tutorial, `notebooks/05_quantum_channel_baseline_comparison.ipynb`,
maps textbook quantum-information channels **and** reduced optical-fiber / free-space
surrogates into **one** executable comparison. It uses:

- a **square 16-QAM** symbol embedding,
- a **pretty-good measurement (PGM)** detector built from the _same_ reference-state
  codebook used at the transmitter (a matched receiver), and
- **BER / SER** as the headline performance metrics,

with a visualization layer that projects the received states onto **constellation**
and **Bloch-sphere** representations for qualitative inspection.

### The channel models

Each channel is a CPTP map $$\mathcal{E}(\rho)$$ acting on the transmitted density
matrix $$\rho$$:

| Channel                | Map / model                                                             | Physical meaning                                  |
| :--------------------- | :---------------------------------------------------------------------- | :------------------------------------------------ |
| **Depolarizing**       | $$\mathcal{E}(\rho) = (1-p)\rho + p\,\tfrac{I}{d}$$                     | Isotropic mixing toward the maximally mixed state |
| **Dephasing**          | $$\mathcal{E}(\rho) = (1-p)\rho + p\,Z\rho Z$$                          | Loss of phase coherence (off-diagonal decay)      |
| **Erasure**            | $$\mathcal{E}(\rho) = (1-p)\rho + p\,\lvert e\rangle\!\langle e\rvert$$ | Symbol flagged/lost with probability $$p$$        |
| **Bosonic (Gaussian)** | Thermal-loss channel, transmissivity $$\eta$$, noise $$\bar n$$         | Optical attenuation + added noise                 |
| **Turbulence (FSO)**   | Málaga / Kolmogorov fading of the transmissivity                        | Free-space atmospheric scintillation              |
| **PMD**                | Random birefringent rotation of the polarization state                  | Fiber polarization-mode dispersion                |

### Matched detection (pretty-good measurement)

For a codebook of reference states $$\{\rho_i\}$$ with priors $$\{p_i\}$$, the PGM
POVM elements are

$$
\Pi_i \;=\; \rho^{-1/2}\,(p_i\rho_i)\,\rho^{-1/2},
\qquad
\rho \;=\; \sum_i p_i\,\rho_i ,
$$

and the decision is $$\hat i = \arg\max_i \operatorname{Tr}(\Pi_i\,\rho_{\text{rx}})$$.
BER/SER then follow from comparing $$\hat i$$ against the transmitted symbols.

---

## Results

For each channel the benchmark exports two views: the **received 16-QAM
constellation** (left) and the corresponding **Bloch-sphere projection** (right). The
constellation shows how each impairment distorts the transmitted symbol grid, while
the Bloch sphere visualizes how pure input states are driven toward mixed states.

### Depolarizing channel

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/constellations_depolarizing.png" title="Depolarizing — received constellation" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/bloch_spheres_depolarizing.png" title="Depolarizing — Bloch sphere" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Depolarizing channel. Isotropic noise contracts the constellation toward the origin
    and shrinks the Bloch vector uniformly toward the maximally mixed state.
</div>

### Dephasing channel

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/constellations_dephasing.png" title="Dephasing — received constellation" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/bloch_spheres_dephasing.png" title="Dephasing — Bloch sphere" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Dephasing channel. Phase coherence decays: the constellation smears along the phase
    direction and the Bloch vector collapses toward the Z-axis.
</div>

### Bosonic (Gaussian) channel

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/constellations_bosonic.png" title="Bosonic — received constellation" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/bloch_spheres_bosonic.png" title="Bosonic — Bloch sphere" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Bosonic Gaussian (thermal-loss) channel. Optical attenuation and added noise scale
    and blur the constellation, modeling fiber/free-space power loss.
</div>

### Turbulence (FSO) channel

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/constellations_turbulence.png" title="Turbulence — received constellation" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/bloch_spheres_turbulence.png" title="Turbulence — Bloch sphere" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Free-space (FSO) turbulence channel. Málaga/Kolmogorov fading of the transmissivity
    produces scintillation-type spreading of the received states.
</div>

### PMD channel (static)

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/constellations_pmd_static.png" title="PMD (static) — received constellation" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/qcomme2e/bloch_spheres_pmd_static.png" title="PMD (static) — Bloch sphere" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Polarization-mode dispersion (static case). A birefringent rotation of the
    polarization state rotates the constellation and the Bloch vector without
    contracting its length.
</div>

## Getting started

```bash
conda env create -f environment.yml
conda activate QCommE2E
pip install -e ".[dev]"

# run the featured experiment
python experiments/fso_turbulence_e2e/run.py

# run the tests
pytest tests/ -v
```

**Repository layout**

```text
QCommE2E/
├── src/quantum_comm_sim/   # Core library
├── config/                 # YAML experiment configs
├── experiments/            # Reproducible experiment scripts
├── notebooks/              # Tutorials (incl. 05_quantum_channel_baseline_comparison)
├── tests/                  # Unit tests
└── docs/                   # architecture.md, channel_models.md
```

The full source, notebooks, and documentation are on
[GitHub → oalnaseri/QCommE2E](https://github.com/oalnaseri/QCommE2E).

---

## Roadmap

The channel comparison is deliberately an **entry point**. Planned/ongoing extensions
include learned **equalization**, **quantum autoencoders** for channel compensation,
and broader **system-level** algorithm integration on top of the same E2E loop.

---

## Publications

If you use QCommE2E, please cite:

- **O. Alnaseri**, _QCommE2E: An Open-Source Simulation of End-to-End Quantum
  Communication Systems_, arXiv:2605.24723 (2026).
  [arXiv](https://arxiv.org/abs/2605.24723)
- **O. Alnaseri**, Y. Himeur, S. Titouni, J. Timmermann, S. Atalla,
  _Fading-Memory Quaternion-Based Kalman Filter for Quantum Channel PMD Compensation_,
  IEEE Access, vol. 14, pp. 46943–46952, 2026.
  [DOI:10.1109/ACCESS.2026.3675938](https://doi.org/10.1109/ACCESS.2026.3675938)

<!--
If both entries exist in _bibliography/papers.bib, `related_publications: true`
(set in the front matter) will auto-list them, and you can cite inline with e.g.
{% cite alnaseri2026qcomme2eopensourcesimulationendtoend %} and
{% cite alnaseri2026fading %}.
-->
