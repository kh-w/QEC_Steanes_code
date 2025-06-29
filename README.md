# Steane's-Code---Quantum-Error-Correction

- [Introduction](#introduction)
- [Logical-state-preparation](#logical-state-preparation)
- [Error-recipe](#error-recipe)

## Introduction

Quantum information is highly susceptible to various sources of error, including decoherence, environmental noise, and imperfect operations. One effective way to protect quantum data, which is similar to classical error correction, is to encode a single qubit into a larger entangled state across multiple physical qubits. This redundancy allows local errors, which affect only some of the qubits, to be detected and corrected without disturbing the underlying logical information. Steane's code is one of the well known quantum correction code, famous for its evolution from the classical $[7,4,3]$ Hamming code, which uses $(7-4)=3$ extra bits to encode (protect) 4 bits of data, with a code distance of 3. It uses the same parity-check matrix to correct both bit-flip and phase-flip errors, resulting in a circuit with 7 physical qubits to encode (protect) 1 logical qubit. The code distance is also 3, as in the classical case, which is the minimum number of physical qubit errors that makes an error on a valid logical state undetectable, i.e., the error transforms one valid logical state into another.

A valid logical state is a superposition of the states in the code space, which is spanned by two orthonormal logical basis states, $\left|0\right\rangle_L$ and $\left|1\right\rangle_L$. The logical basis state $\left|0\right\rangle_L$​ is an equal superposition of the quantum versions of the classical Hamming codewords, and $\left|1\right\rangle_L=X^{\otimes7}\left|0\right\rangle_L$​. The error correction procedure is also similar to that of the classical Hamming code, but corrections must be made for both bit flips and phase flips.

## Logical-state-preparation

The logical basis state $\left|0\right\rangle_L$​ is an equal superposition of the quantum versions of the classical Hamming codewords:

$|0_L\rangle = \frac{1}{\sqrt{8}}\sum_{x\in\text{Hamming Codewords}}\left|x\right\rangle\$

where I used a [spreadsheet](https://github.com/kh-w/QEC_Steanes_code/blob/main/steanes_code_stabilizer.xlsx) to illustrate how to obtain the codewords using the stabilizers. 

Procedures are as followed:
- Write down the parity-check matrix $H$ of the Hamming code.

  ![image](https://github.com/user-attachments/assets/960dc107-b464-4473-932d-a9b841d4200b)
- Create all 128 basis states $\left|x\right\rangle$ of a 7-qubit.
- Get the dot products $H\cdot x$.
- The $\left|x\right\rangle$ with zero dot products, i.e. $(0, 0, 0)^T$, are in the code space.
- In addition, the $\left|x\right\rangle$ with zero dot products AND with even number of 1s are the logical basis states of $|0_L\rangle$.

As a result, I obtained:

$|0_L\rangle = \frac{1}{\sqrt{8}}(\left|0000000\right\rangle\+\left|0111100\right\rangle\+\left|1011010\right\rangle\+\left|1100110\right\rangle\+\left|1101001\right\rangle\+\left|1010101\right\rangle\+\left|0110011\right\rangle\+\left|0001111\right\rangle\)$

It will be implemented in the circuit manually instead of using a collection of Pauli gates. 

## Error-recipe

We never measure the logical qubit directly during quantum error correction, as this would cause the quantum state to collapse. Instead, it is standard practice to use ancilla qubits to store the results of error checks, known as syndromes. In the circuit I am building, three ancilla qubits will be used to record the bit-flip syndromes (calculated as dot products modulo 2), and another three ancillas will record the phase-flip syndromes (also dot products modulo 2). These ancillas are then measured to obtain the syndrome values, which are encoded as classical bits. The resulting classical bits represent integers from 0 to 7, each corresponding to a specific error that can be identified and corrected:

![image](https://github.com/user-attachments/assets/81a850c7-b1d6-4126-a097-e22277b1d45c)

The same recipe will be used for both $X$ and $Z$ errors.

## Circuit

![image](https://github.com/user-attachments/assets/5a3e0095-6cac-47a2-95ab-be3a49d6bca8)

(Click to enlarge)

Citcuit description:

1st-7th wires: The 7-qubit logical quantum state.
8-10th wires: $X_0$, $X_1$ and $X_2$ to check bit flips, they are storing the dot products (mod 2) of the 7-qubit with stabilizers XIXIXIX, IXXIIXX, IIIXXXX respectively.
11-13th wires: $Z_0$, $Z_1$ and $Z_2$ to check phase flips, they are storing the dot products (mod 2) of the 7-qubit with stabilizers ZIZIZIZ, IZZIIZZ, IIIZZZZ respectively.
14th wire: Store the measured 8-10th wires, correction will be based on this and the error recipe.
15th wire: Store the measured 11-13th wires, correction will be based on this and the error recipe.
16th wire: Measure the corrected 7-qubit and store the result here.

Step 1: Prepare the logical quantum state $|0_L\rangle$ described in section [Logical-state-preparation](#logical-state-preparation).
Step 2: Inject random errors, in our case, 3 small random errors on random qubits, 1 big large fixed error on a fixed qubit.
Step 3: Perform the dot products (mod 2) of the 7-qubit with stabilizers XIXIXIX, IXXIIXX, IIIXXXX and store.
Step 4: Perform the dot products (mod 2) of the 7-qubit with stabilizers ZIZIZIZ, IZZIIZZ, IIIZZZZ and store.
Step 5: Measure the syndromes and store.
Step 6: Perform bit flip error correction based on the value of 14th wire according to the error recipe.
Step 7: Perform phase flip error correction based on the value of 15th wire according to the error recipe.
Step 8: Measure the corrected 7-qubit and store.

