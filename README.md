# Steane's-Code---Quantum-Error-Correction

- [Introduction](#introduction)
- [Logical-state-preparation](#logical-state-preparation)

## Introduction

Steane's code is well known for its evolution from the classical $[7,4,3]$ Hamming code, which uses $(7-4)=3$ extra bits to encode (protect) 4 bits of data, with a code distance of 3. It uses the same parity-check matrix to correct both bit-flip and phase-flip errors, resulting in a circuit with 7 physical qubits to encode (protect) 1 logical qubit. The code distance is also 3, as in the classical case, which is the minimum number of physical qubit errors that makes an error on a valid logical state undetectable, i.e., the error transforms one valid logical state into another.

A valid logical state is a superposition of the states in the code space, which is spanned by two orthonormal logical basis states, $\left|0\right\rangle_L$ and $\left|1\right\rangle_L$. The logical basis state $\left|0\right\rangle_L$​ is an equal superposition of the quantum versions of the classical Hamming codewords, and $\left|1\right\rangle_L=X^{\otimes7}\left|0\right\rangle_L$​. The error correction procedure is also similar to that of the classical Hamming code, but corrections must be made for both bit flips and phase flips.

## Logical-state-preparation

The logical basis state $\left|0\right\rangle_L$​ is an equal superposition of the quantum versions of the classical Hamming codewords:

$|0_L\rangle = \frac{1}{\sqrt{8}}\sum_{x\in\text{Hamming Codewords}}\left|x\right\rangle\$

where I used a [spreadsheet](https://github.com/kh-w/QEC_Steanes_code/blob/main/steanes_code_stabilizer.xlsx) to illustrate how to obtain the codewords using the stabilizers. 
