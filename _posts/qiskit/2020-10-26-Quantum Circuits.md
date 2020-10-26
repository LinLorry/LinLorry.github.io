---
layout: post
category: qiskit
---

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    jax: ["input/TeX", "output/HTML-CSS"],
    tex2jax: {
      inlineMath: [ ['$', '$'], ["\\(", "\\)"] ],
      displayMath: [ ['$$', '$$'], ["\\[", "\\]"] ],
      processEscapes: true,
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
    }
  });
</script>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML" type="text/javascript"></script>

### Circuit model

**Circuit model** is a sequence of some building blocks that carry out our computations, called **gates** ![](/assets/img/qiskit/Quantum Circuits gate.png)

#### Single qubit gates

- classical example: NOT ![](/assets/img/qiskit/Not Gate.png)

- quantum example: as quantum theory is **unitary**, quantum gates are represented by **unitary matrices**: $u_+ u = 1$

direct notation:

$$
\begin{pmatrix}
u_{00} & u_{01} \\
u_{10} & u_{11}
\end{pmatrix} = u_{00} \vert 0 \rangle \langle 0 \vert + u_{01}\vert 0 \rangle \langle 1 \vert + u_{10}\vert 1 \rangle \langle 0 \vert + u_{11}\vert 1 \rangle \langle 1 \vert
$$

- $$
  \sigma_x = \begin{pmatrix} 0 & 1 \\ 1 & 0 \end{pmatrix} = \vert 0 \rangle \langle 1 \vert + \vert 1 \rangle \langle 0 \vert
  $$

  $$
  \sigma_x \vert 0 \rangle =
  \begin{pmatrix}
  0 & 1 \\
  1 & 0
  \end{pmatrix}
  \cdot
  \begin{pmatrix} 1 \\ 0  \end{pmatrix} =
  \begin{pmatrix} 0 \\ 1 \end{pmatrix} =
  \vert 1 \rangle
  $$

  $$
  \sigma_x \vert 1 \rangle =
  (
    \vert 0 \rangle \langle 1 \vert +
    \vert 1 \rangle \langle 0 \vert
  )
  \cdot \vert 1 \rangle =
  \vert 0 \rangle \langle 1 \vert 1 \rangle +
  \vert 1 \rangle \langle 0 \vert 1 \rangle =
  \vert 0 \rangle
  $$

  => bit flip $\cong$ NOT-gate. e.g. ![](/assets/img/qiskit/sigma_x_gate.png)
  => rotation around the X axis by $\pi$.

- $$
  \sigma_z = \begin{pmatrix} 1 & 0 \\ 0 & -1 \end{pmatrix} = \vert 0 \rangle \langle 0 \vert - \vert 1 \rangle \langle 1 \vert
  $$
