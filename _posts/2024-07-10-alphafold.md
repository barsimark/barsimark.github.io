---
layout: AlphaFold
title: Highly accurate protein structure prediction with AlphaFold
description: Blogpost based on Highly accurate protein structure prediction with AlphaFold article by Nature
tags: AlphaFold protein blogpost
giscus_comments: true
date: 2024-07-10
featured: true

authors:
  - name: Mark Barsi
    affiliations:
      name: University of Stuttgart

toc:
  - name: Short summary

_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
---

## Short summary

Protein structures are fundamental to biological research, yet the known protein structures represent a tiny fraction of the vast diversity in existence. Traditional methods for determining protein structures through computational means, such as physical interactions and evolutionary history, are complex and time-consuming. To address these challenges, AlphaFold, a neural network model, was introduced in 2021, offering near-experimental accuracy in predicting three-dimensional protein structures. AlphaFold leverages a two-part architecture: Evoformer blocks and structure modules. Evoformer blocks predict relationships between protein components using graph inference, while structure modules calculate explicit three-dimensional structures through iterative refinement. The model is trained using the Protein Data Bank and self-distillation datasets, with additional genetic and structure databases for multiple sequence alignments. AlphaFold’s training involves a two-stage process with both initial and fine-tuning phases, achieving impressive accuracy. Results demonstrate the model’s capability in predicting both simple and complex protein structures with high precision. The AlphaFold model’s public availability has spurred further research, with significant implications for medical and biological sciences, such as developing vaccines, understanding genetic diseases, and combating antibiotic resistance. Since its publication, AlphaFold has been highly influential, evidenced by numerous citations and widespread recognition in the scientific community.
