# Steane's-Code---Quantum-Error-Correction

- [Introduction](#introduction)
- [Logical-state-preparation](#logical-state-preparation)
- [Error-recipe](#error-recipe)
- [Circuit](#circuit)
  - [Circuit-description](#circuit-description)
- [Result](#result)

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
- Get the dot products $H\cdot x$ for all $x$.
- The $x$ with zero dot products, i.e. $(0, 0, 0)^T$, are in the code space.
- In addition, the $\left|x\right\rangle$ with zero dot products AND with even number of 1s are the logical basis states of $|0_L\rangle$.

As a result, I obtained:

$|0_L\rangle = \frac{1}{\sqrt{8}}(\left|0000000\right\rangle\+\left|0111100\right\rangle\+\left|1011010\right\rangle\+\left|1100110\right\rangle\+\left|1101001\right\rangle\+\left|1010101\right\rangle\+\left|0110011\right\rangle\+\left|0001111\right\rangle\)$

$|1_L\rangle = \frac{1}{\sqrt{8}}(\left|1111111\right\rangle\+\left|1000011\right\rangle\+\left|0100101\right\rangle\+\left|0011001\right\rangle\+\left|0010110\right\rangle\+\left|0101010\right\rangle\+\left|1001100\right\rangle\+\left|1110000\right\rangle\)$

It will be implemented in the circuit manually instead of using a collection of Pauli gates because we are not going to decode the corrected 7-qubit at the end. If decoding is needed, an encoding circuit is needed and the decode circuit would be the inverse of the encoding circuit.

## Error-recipe

We never measure the logical qubit directly during quantum error correction, as this would cause the quantum state to collapse. Instead, it is standard practice to use ancilla qubits to store the results of error checks, known as syndromes. In the circuit I am building, three ancilla qubits will be used to record the bit-flip syndromes (calculated as dot products modulo 2), and another three ancillas will record the phase-flip syndromes (also dot products modulo 2). These ancillas are then measured to obtain the syndrome values, which are encoded as classical bits. The resulting classical bits represent integers from 0 to 7, each corresponding to a specific error that can be identified and corrected:

![image](https://github.com/user-attachments/assets/81a850c7-b1d6-4126-a097-e22277b1d45c)

The same recipe will be used for both $X$ and $Z$ errors.

## Circuit

![image](https://github.com/user-attachments/assets/5a3e0095-6cac-47a2-95ab-be3a49d6bca8)

(Click to enlarge)

### Circuit-description

#### From-top-down
- 1st-7th wires: The 7-qubit logical quantum state.
- 8-10th wires: $X_0$, $X_1$ and $X_2$ to check bit flips, they are storing the dot products (mod 2) of the 7-qubit with stabilizers XIXIXIX, IXXIIXX, IIIXXXX respectively.
- 11-13th wires: $Z_0$, $Z_1$ and $Z_2$ to check phase flips, they are storing the dot products (mod 2) of the 7-qubit with stabilizers ZIZIZIZ, IZZIIZZ, IIIZZZZ respectively.
- 14th wire: Store the measured 8-10th wires, correction will be based on this and the error recipe.
- 15th wire: Store the measured 11-13th wires, correction will be based on this and the error recipe.
- 16th wire: Measure the corrected 7-qubit and store the result here.

#### From-left-to-right
- Step 1: Prepare the logical quantum state $|0_L\rangle$ described in section [Logical-state-preparation](#logical-state-preparation).
- Step 2: Inject random errors, in our case, 3 small random errors on random qubits, 1 big large fixed error on a fixed qubit.
- Step 3: Perform the dot products (mod 2) of the 7-qubit with stabilizers XIXIXIX, IXXIIXX, IIIXXXX and store.
- Step 4: Perform the dot products (mod 2) of the 7-qubit with stabilizers ZIZIZIZ, IZZIIZZ, IIIZZZZ and store.
- Step 5: Measure the syndromes and store.
- Step 6: Perform bit flip error correction based on the value of 14th wire according to the error recipe.
- Step 7: Perform phase flip error correction based on the value of 15th wire according to the error recipe.
- Step 8: Measure the corrected 7-qubit and store.

## Result

When an error affects a valid logical state, it typically transforms the state into a superposition that includes basis states outside the code space. As a result, if we measure the system, it may collapse to a state that is not part of the code space, indicating that an error has occurred. This phenomenon can be observed by disabling the error correction steps in the circuit and running it for 50,000 repetitions to produce a histogram of measurement outcomes. The presence of out-of-code-space results reveals the impact of uncorrected errors.

### Before:
![image](https://github.com/user-attachments/assets/3d41c5a8-6c7f-4852-b010-cd0a3be5d385)

The 16 tall bars represent the valid basis states within the code space. The remaining shorter bars correspond to states outside the code space, indicating the presence of uncorrected errors. 

### After:
![image](https://github.com/user-attachments/assets/50ede470-77ae-4a8c-84b8-0e7cc9363a89)

The 16 valid basis states remain, but the short bars have disappeared! This shows that all measurements now fall within the code space, confirming that the error correction is successfully working.

### Success probabilty
<img width="420" height="281" alt="image" src="https://github.com/user-attachments/assets/9b601815-4e10-4861-9503-e851cdbf5e2f" />


