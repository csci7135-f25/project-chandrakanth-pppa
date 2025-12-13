[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/KIMhwtUS)
# Certifying Differential Invariants of Neural Networks using Abstract Duals

This repository contains the course project for CSCI 7135 - Principles and Practice of Programming Analysis. The project focuses on proving robustness of neural networks using differential invariants and abstract duals. The implementation is based on the `ocaml-nn` framework.

## Organization

This repository is organized as follows:

```
.
├── code
│   ├── nn-static-analysis (sub-module)
│   └── README.md
├── paper
│   ├── sections
│   ├── Makefile
│   ├── paper.bib
│   ├── paper.pdf
│   ├── paper.tex
│   └── README.md
├── talk
│   ├── main.pdf
│   └── README.md
└── README.md
```

## Assignment

### Repository Instructions

Organize your project into the following directories:
- [code](code/) for the implementation;
- [paper](paper/) for the project paper; and
- [talk](talk/) for the project presentation slides.

### Paper organization

The LaTeX source for the project paper is located in the `paper/` directory. The main tex file is `paper.tex`, which includes sections from the `sections/` sub-directory and the bibliography file `paper.bib`. To compile the paper, run `make` in the `paper/` directory.

### Talk organization

The presentation slides for the project talk are located in the `talk/` directory. The main file is `main.pdf`.

### Code organization

The implementation for the project is located in the `code/` directory. It includes a sub-module `nn-static-analysis` which contains the core static analysis framework for neural networks. The `README.md` file in the `code/` directory provides further details on how to build and run the code.