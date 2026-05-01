---
layout: single
title: "Research Outputs"
permalink: /research/
---

Quantum computing is showing promise in solving combinatorial optimisation problems that are hard to tackle classically. These problems are ubiquitous in various fields including, but not limited to, machine learning, finance, and bioinformatics. Quantum algorithms such as Quantum Annealing (QA), Grover search and Variational Quantum Algorithms (VQA) have been able to show theoretical speed-ups in the time complexity for combinatorial optimisation problems. The bioinformatics domain inherently includes many NP-hard combinatorial optimisation problems such as genome assembly, sequence alignment, metagenomic binning, etc. Current high performance computing (HPC) solutions still require days to solve a genome assembly problem, where time could be a crucial factor in events like a viral outbreak. The hope is that quantum approaches could significantly reduce the time to solutions for these types of problems. To encode these problems for a quantum solver, they can be transformed into Quadratic Unconstrained Binary Optimisation (QUBO) embeddings.

The QUBO model provides a unified framework for formulating many NP-Hard combinatorial optimization problems such as the Travelling Salesman Problem (TSP), graph partitioning, and scheduling which are difficult to scale using classical exact solvers. Although there are specialized heuristics (e.g., Lin–Kernighan for TSP), they lack generality. In contrast, general-purpose QUBO solvers, including IBM Cplex and other MILP/QP engines, offer flexibility but struggle with large instances, with more than 100 binary variables. QUBO also has the advantage of being represented as a quantum energy Hamiltonian naturally, which can then be optimised via quantum computing and classical Simulated Annealing (SA). Many quantum algorithms provide multiple optimal solutions in parallel, by taking advantage of quantum superposition. This is useful for many applications such as cancer genomics, where identifying variants becomes important. 

Several challenges occur consistently when performing quantum experiments that impact the usability of current quantum devices, and especially QUBO based optimisation, for real world tasks:

*  Current Noisy Intermediate-Scale Quantum (NISQ) devices have a limited number of qubits and cannot (yet) be used for practical and scalable applications. NISQ devices are characterised by qubits prone to errors that lose their quantum state (decoherence) due to environmental interference.
*  A quantum circuit is a graphical model representing a sequence of quantum gates, measurements, and resets applied to qubits to perform computations. Operating on principles of quantum mechanics, they use gates (e.g., Hadamard, CNOT) to manipulate superpositions and entanglement. Gate fidelities of current devices for single-qubit operations are around $99-99.5\%$ and for two-qubit gates around $95–99\%$, which introduce significant errors in circuits that compounds as number of operations increase.
*  QUBO encodings can dramatically increase the number of binary variables required to represent a combinatorial optimisation problem, impacting scalability in simulation and on real devices. This affects large scale experiments to benchmark quantum algorithms on real devices as well as ideal simulations on classical devices.
*  QUBO is a generalized approach which works well for certain problems like Max-Cut. For problems like the TSP, there are strict constraints (e.g., each city must be visited exactly once, valid tour order). Since QUBO is unconstrained, these constraints must be added as penalty terms in the objective function. These penalties only encourage the constraints rather than enforce them perfectly, so the model may produce approximate or invalid solutions if penalties are not tuned properly.

Genome assembly is the computational process of reconstructing a genome sequence from many shorter sequencing reads. Modern genome assembly algorithms typically represent reads using graph based models in which nodes correspond to reads or sequence fragments and edges represent overlaps between sequences. Two dominant paradigms are used: overlap graphs and de Bruijn graphs. Overlap graph approaches form the basis of the Overlap–Layout–Consensus (OLC) framework. In this paradigm, overlaps between reads are first identified, a graph describing read relationships is constructed, and a consensus sequence is generated from a traversal of the resulting graph. 

In overlap graphs, reads are represented as vertices and edges correspond to suffix–prefix overlaps. Recovering the genome therefore requires identifying a path that visits reads in the correct order. Under common formulations, this corresponds to solving a TSP on the overlap graph. Real sequencing data introduces additional challenges such as sequencing errors and repeated genomic regions introduce spurious edges in the assembly graph, making it difficult to identify the correct path corresponding to the true genome sequence. These structural challenges motivate the development of advanced optimisation techniques for navigating assembly graphs. Since this problem is NP-hard, no polynomial time algorithm is known for solving it in the general case. Because of this complexity, most practical assemblers rely on heuristics and graph simplification techniques. The alternative de Bruijn graph approach, where reads are decomposed into smaller $k$-mers and assembly corresponds to finding an Eulerian path through the graph. Eulerian path problems can be solved in linear time, which explains the practical success of de Bruijn graph assemblers. However, this representation introduces other challenges such as sensitivity to sequencing errors and difficulty resolving long repeats. The combinatorial nature of genome assembly therefore motivates exploring alternative optimisation paradigms capable of efficiently searching large solution spaces. Quantum computing has emerged as a potential framework for tackling such optimisation problems.

## Scalable Quantum Optimisation using HADOF
HADOF iteratively decomposes a global quantum Hamiltonian into small sub-Hamiltonians and refines a global solution using sampled distributions. HADOF is compatible with optimisation methods that return sampleable distributions biased toward good solutions. This allows it to scale beyond available qubits while preserving access to multiple good candidate solutions.

<img width="3780" height="1890" alt="overview" src="https://github.com/user-attachments/assets/caf2e638-2497-42f4-915a-6319a40e9b2c" />


**General Overview** 

HADOF proceeds iteratively: 

1) Encode the full QUBO as a Hamiltonian.
   
2) Solve small sub-Hamiltonians using a sampling-based optimiser.
   
3) Estimate marginal variable probabilities from samples.

4) Update the sub-Hamiltonians using this global information.

5) Aggregate sampled sub-solutions into global candidate solutions.

HADOF scales to problem sizes more than 12x beyond the native qubit capacity of current devices with 40x higher accuracy that currently used standard QAOA on NISQ devices. Empirically, HADOF demonstrates strong scalability properties. We demonstrated the optimisation of QUBO problems up to 500 binary variables using only 5 qubit circuits by decomposition, while maintaining above 95\% accuracy compared to classical Simulated Annealing (in traditional quantum methods such as QAOA, this would require 500 qubits in an ideal noise-free setting). 

## Parallelisation and Quantum Supercomputing

<img width="709" height="480" alt="ttsquantum" src="https://github.com/user-attachments/assets/7311e06f-61a5-41a5-936d-2620772d1844" />


To further improve scalability and runtime, HADOF was extended to a parallel execution model, forming a High Performance Quantum (HPQ) framework, where sub-Hamiltonians are generated independently and solved concurrently. Unlike the sequential variant, which updates marginal probabilities after each subproblem, the parallel version uses marginal estimates from the previous iteration, enabling asynchronous execution. This design transforms each iteration into a collection of independent sub-circuits, allowing efficient utilisation of classical multi-core systems and multiple quantum processing units (QPUs). Parallelisation reduces wall clock time by 3-5x without degrading the quality of the best solutions, as observed in the benchmarking experiments on IBM QPUs. Importantly, results indicate that HPQ preserves the optimisation landscape’s high quality regions while improving computational efficiency. These findings validate parallel decomposition as a viable strategy for scaling quantum optimisation.

## Quantum Genome Assembly

<img width="1536" height="905" alt="Assembly-2-2" src="https://github.com/user-attachments/assets/effba69b-e7b9-47be-91cb-642b41a8365b" />


Building on the scalability of HADOF, the framework was applied to genome assembly, formulated as a Hamiltonian path problem on string graphs. This represents a significant step beyond prior work, which has been limited to small synthetic datasets. Preliminary simulated experiments demonstrate that HADOF can effectively solve assembly problems with increasing graph sizes. Accuracy is evaluated using mis-edge counts and post-processed path lengths, showing that HADOF produces high quality solutions. Further analysis showed that HADOF-QAOA with small circuits outperforms standard QAOA on real hardware, highlighting the advantage of decomposition under noise. Crucially, HADOF enables solving problems beyond hardware limits. The framework was applied to real sequencing data from *Pseudomonas aeruginosa* and integrated into standard assembly practice. The genome consists of 7.3 million base pairs, which is a significant step-up in size compared to current simulated datasets which show examples scaling up to 5 thousand base pairs. The corresponding overlap graph contains 524 nodes and 2313 edges, requiring 2313 noise-free qubits for accurate optimisation, far exceeding the capabilities of current quantum devices (around 150 qubits for gate based devices). By leveraging HADOF-based decomposition, the problem becomes tractable through federated optimisation. 
