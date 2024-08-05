---
layout: post
title: "Quantum gates"
subtitle: "Quantum computing, part 2"
thumbnail-img: /assets/img/quantum.png
share-img: /assets/img/quantum.png
tags: [quantum]
---

This is part of a series in which I learn about basic quantum computing from a series YouTube [lectures](https://www.youtube.com/watch?v=vfjN7MmSB6g&list=PLkvhuSoxwjI_UudECvFYArvG0cLbFlzSr). Last time, I covered how quantum computers store information as qubits. This post will introduce how to perform computations on qubits, using quantum circuits. Once again, we'll see how computation works on classical computers, and then extend to the quantum analogue.

## Classical logic gates
In classical computing, we can perform operations on bits by running them through logic gates. Many of these logic gates, such as $$\text{NOT}$$, $$\text{AND}$$, and $$\text{XOR}$$, should already look quite familiar. By combining multiple gates into a circuit, we can perform more complex computations, and even implement logical switches like if-else statements. But how can we represent logic gates mathematically?

Since classical computers store information as vectors of bits, we can express operations on them as linear maps over the boolean field $$\mathbb{F}_2$$. Explicitly, suppose a logic gate $$f$$ takes $$n$$ input bits to $$m$$ output bits; we can express it as a $$2^m \times 2^n$$ matrix, where column $$i$$ contains $$f(\vert i \rangle)$$. 

Let's write some familiar logic gates as linear maps! Perhaps the simplest gate, $$\text{NOT}$$ sends $$\vert 0 \rangle \to \vert 1 \rangle$$ and $$\vert 1 \rangle \to \vert 0 \rangle$$. Therefore its matrix representation is

$$
\text{NOT} = \begin{pmatrix}
0 & 1 \\
1 & 0
\end{pmatrix},
$$

because column $$0$$ contains $$\text{NOT}(\vert 0 \rangle) = \vert 1 \rangle$$, and column $$1$$ contains $$\text{NOT}(\vert 1 \rangle) = \vert 0 \rangle$$. 

Moving on, the $$\text{AND}$$ gate receives multiple input bits. It maps $$\vert 00 \rangle, \vert 10 \rangle, \vert 10 \rangle \to \vert 0 \rangle$$, and $$\vert 11 \rangle \to \vert 1 \rangle$$. In matrix form,

$$
\text{AND} = \begin{pmatrix}
1 & 1 & 1 & 0 \\
0 & 0 & 0 & 1
\end{pmatrix}.
$$

Similarly, we can write the $$\text{XOR}$$ gate as

$$
\text{XOR} = \begin{pmatrix}
1 & 0 & 0 & 1 \\
0 & 1 & 1 & 0
\end{pmatrix}.
$$

Classical computers perform more complex operations by combining these gates in series and parallel into computational *circuits.* By applying gates one after the other to the input bits, we can transform them into desired outputs.

## Quantum gates
Now that we understand classical logic, let's compare it with the quantum analog. 

Like classical gates, quantum gates are linear maps that act on state vectors. But since qubits live in complex space, the underlying field is $$\mathbb{C}$$ rather than $$\mathbb{F}_2$$. Since quantum states are *unit* vectors, quantum gates must also map unit vectors to unit vectors. Moreover, quantum gates should preserve the inner product between vectors, so that distinguishable quantum states remain distinguishable after transformation. Mathematically, this means that quantum gates are *unitary* transformations.

There are some interesting consequences of this postulate. We know that a unitary transformation $$U$$ satisfies $$U^* U = U U^* = I$$; that is, the map is its own inverse. In particular, each gate in a quantum circuit is invertible. And since we make quantum circuits by stringing a bunch of gates together, the same is true for every quantum circuit. A quantum computer can only perform reversible computations; given the output, and a circuit diagram for our computer, we should be able to figure out exactly what the input was!

This idea is new to quantum computing because not all classical gates are reversible. If I tell you that an $$\text{AND}$$ circuit produced $$\vert 0 \rangle$$, there's no way to figure out whether the input was $$\vert 00 \rangle, \vert 01 \rangle$$, or $$\vert 10 \rangle$$. Nonetheless, any classical circuit can be transformed into a reversible one, by keeping a copy of the input, copying the output onto a new wire, then reversing the computation on the original circuit. 

![reverse](/assets/img/reversible.png){: .mx-auto.d-block :}

Alternatively, we can simulate classical gates using Toeffeli gates. The Toeffeli gate $$T$$ performs the operation $$T(x, y, z) = (x, y, xy \oplus z)$$, where $$\oplus$$ denotes the $$\text{XOR}$$ operation. Not only are Toeffeli gates reversible, but they're also universal; they can simulate $$\text{AND}$$ and $$\text{OR}$$ gates, and by extension, any classical circuit. Therefore, quantum computers can in principle do everything that classical computers can.

### Examples of gates
There are a couple of other quantum gates that appear frequently in quantum algorithms and will be handy later on:
- The $$\text{CNOT}$$ gate sends $$\text{CNOT}(x,  y) = (x, x \oplus y)$$. Its matrix representation is

    $$
    \text{CNOT} = \begin{pmatrix}
    1 & 0 & 0 & 0 \\
    0 & 1 & 0 & 0 \\
    0 & 0 & 0 & 1 \\
    0 & 0 & 1 & 0
    \end{pmatrix}.
    $$

    This gate is important because it performs the quantum analog of logical control switching; it flips $$y$$ if $$x$$ is on, but otherwise doesn't do anything. We can think of $$x$$ as the switch condition in a classical if-else block.

- The Hadamard gate is useful for creating superpositions from basis states, as it sends $$\vert 0 \rangle \to \vert + \rangle, \vert 1 \rangle \to \vert - \rangle$$. In matrix form: 

    $$
    H = \frac{1}{\sqrt{2}}\begin{pmatrix}
    1 & 1 \\
    1 & -1
    \end{pmatrix}.
    $$

- The Pauli gates,

    $$
    X = \frac{1}{\sqrt{2}}\begin{pmatrix}
    0 & 1 \\
    1 & 0
    \end{pmatrix},
    $$

    $$
    Y = \frac{1}{\sqrt{2}}\begin{pmatrix}
    0 & -i \\
    i & 0
    \end{pmatrix},
    $$

    $$
    Z = \frac{1}{\sqrt{2}}\begin{pmatrix}
    1 & 0 \\
    0 & -1
    \end{pmatrix},
    $$
    
    correspond to rotating the Bloch sphere by $$\tau/2$$ radians around the $$X, Y, Z$$ axes respectively. As a gentle reminder, here's what the Bloch sphere looks like.

![bloch](/assets/img/qubit.jpg){: .mx-auto.d-block :}

- The phase shift gate rotates the Bloch sphere around the $$Z$$ axis by $$\theta$$ radians: 

    $$R(\theta) = \frac{1}{\sqrt{2}}\begin{pmatrix}
    1 & 0 \\
    0 & e^{i \theta}.
    \end{pmatrix}$$

    As the name suggests, this corresponds to shifting the angle between the two complex coordinates of a qubit.

### Quantum circuits

Just like in the classical world, we can combine multiple gates into a quantum circuit. If we apply gates $$X$$ and $$Y$$ in parallel, we get the tensor product $$X \otimes Y$$. And if we apply gates in series, first $$X$$ then $$Y$$, we get the composition $$Y \circ X$$. As a concrete example, if we start out with two zero qubits, we can produce a Bell state by running them through the circuit $$\text{CNOT} \circ (H \oplus I)$$. Explicitly, we can draw a diagram for this circuit.

![bell](/assets/img/bell.png){: .mx-auto.d-block :}

We can also express the computation performed in this diagram purely in terms of algebra:

$$
\begin{align}
\text{CNOT} \circ (H \oplus I) (\vert 00 \rangle) &= \text{CNOT}\left(\frac{1}{\sqrt{2}}(\vert 00 \rangle + \vert 10 \rangle)\right) \\
&= \frac{1}{\sqrt{2}}(\text{CNOT}(\vert 00 \rangle) + \text{CNOT}(\vert 10 \rangle)) \\
&= \frac{1}{\sqrt{2}}(\vert 00 \rangle + \vert 11 \rangle).
\end{align}
$$

With that, we've finished describing how quantum computers store information in qubits, and how we can process those qubits by running them to quantum circuits. Each quantum gate is a unitary linear transformation, and we can combine them into circuits by performing function composition and tensor products. Quantum gates can simulate any classical gates, so quantum computers are at least as powerful as classical computers. Next time, I might talk about some toy problems that demonstrate what these computers can or can't do.