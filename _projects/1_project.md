---
layout: page
title: Mobile Communications Course — Interactive Simulations
description: Interactive Jupyter/HTML simulations that make core mobile communications concepts — multipath propagation and Huygens diffraction — easier to understand, based on my university teaching material.
img: assets/img/mobilcom/cover.png
importance: 1
category: work
github: https://github.com/oalnaseri/mobilcom_course
img: assets/img/mobilcom/cover.png
---

A collection of **interactive simulations** designed to make mobile communications
topics easier to understand. They are based on the material I teach at the university —
explore them, tweak the parameters, and watch the physics respond in real time.

## What's inside

- **Multipath propagation** — an interactive notebook (and standalone HTML) showing how
  signals combine over multiple paths and how the resulting interference shapes the
  received signal.
- **Huygens diffraction** — an interactive visualization of wavefront propagation and
  diffraction from Huygens' principle.

Each topic ships as both a Jupyter notebook (with `ipywidgets` sliders) and a
self-contained HTML export, so they can be run live or viewed directly in the browser.

## Getting started

```bash
conda create -n mobilcom python=3.10
conda activate mobilcom
pip install matplotlib numpy
conda install -c conda-forge jupyterlab ipywidgets
jupyter lab
```

More simulations are on the way. The full material is on
[GitHub → oalnaseri/mobilcom_course](https://github.com/oalnaseri/mobilcom_course).
