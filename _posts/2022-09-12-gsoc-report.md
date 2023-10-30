---
title: 'Final submission report for GSoC 2022'
date: 2022-09-12
permalink: /posts/2013/08/gsoc-report/
tags:
  - convolutions
  - mpi
  - gsoc
---

---

## Details

|  |  |
| --- | --- |
| Name | [Pratham Shah](https://github.com/shahpratham) |
| Organisation | [Forschungszentrum Jülich](https://www.fz-juelich.de/en) |
| Mentor | [Claudia Comito](https://github.com/ClaudiaComito) |
| Project | [RFI Mitigation](https://summerofcode.withgoogle.com/programs/2022/projects/lPOgRLTX) |

## Table of Contents

- [About Project](#about-project)
- [Implementation](#implementation)
- [Summary of work done](#summary-of-work-done)
- [Contributions](#contributions)
- [Future Plan](#future-plan)
- [Conclusion](#conclusion)

## About Project

Heat is a distributed tensor framework that aims at making it easier for researchers to port their NumPy applications to HPC (high-performance computing) architectures. Among other things, Heat is preparing to support the data-intensive radioastronomy  community. This project explores the use of image analysis in a two-dimensional spectrum to detect RFI (Radio Frequency Interference) in large amounts of memory-distributed radioastronomical data. As a result, this project supports and develops 1D and 2D memory-distributed convolution within the Heat domain decomposition (or data distribution) scheme.

**Deliverables:**
- Expand current implementation to support fully distributed (signal and kernel) 1D convolution
- Expand current implementation to support fully distributed (signal and kernel) 2D convolution
- Ensure compatibility/consistency with numpy.convolve and scipy.signal.convolve2d
- Test and document features


## Implementation

Heat's DNDarray is an N-dimensional data structure composed of computational objects on one or more processes. The process local objects are Pytorch tensors, allowing it to run on CPUs as well as GPUs. DNDarray is used to accelerate computations in distributed cluster systems using MPI which controls communication among parallel processes on distributed memory systems via a set of message sending and receiving functions.

Heat's current implementation of 1D and 2D convolution only supports convolutions on one distributed DNDarray, the signal, with a non-distributed DNDarray, the filter. However, because the user-defined procedure involves a 2D convolution of a large matrix with its own boolean mask, we need both the signal and the kernel to be distributed in memory. As a result, I had to create a new method to distribute the convolution operation using MPI.

### Dealing with distributed kernel
<img src="https://user-images.githubusercontent.com/82367556/189073980-4818ffb2-d7eb-474b-92a0-d7af8941cce7.png" alt="conv-sketch"/>

This is a rough sketch of how the operation looks like.

The convolutions operation with distributed kernel consisted of :
- Compute `t_v`, the local weight tensor (Incase of uneven distribution, pad local chunk with zeros).
- Broadcast `t_v` and perform local convolution operation (Send all local tensors to all the processes serially).
- Accumulate filtered signal (Store the convolution result in global_signal_filtered on the fly).

So, when the kernel v is distributed, we must find the convolution of all distributed kernels with signals and store it in a DNDarray. Later, compute the filtered signal by slicing a chunk of the size of our output's (convolution) shape from each row of the DNDarray and adding them along its column to get our result.

## Summary of work done
- A custom method was devised to parallelize 1D and 2D convolution. APIs for both implementations are consistent with `numpy.convolve()` and `scipy.signal.convolve2d()` and provide appropriate functionality.
- Support for all pre-existing modes options(i.e. "full", "same" and "valid") was added.
- All edge cases, as well as all combinations of different modes and split axes of distributed DNDarrays, were tested. I used NumPy and SciPy to validate the above implementation on large random arrays. All novel functions for convolutions are provided with documentation.
- Performance was improved by adopting MPI collective communication (Bcast)  instead of point-to-point non-blocking communication (Isend and Irecv) among the processes.
- Documentations and correctness tests were added for all the code implemented.

## Contributions

### Pull requests Issued

- [#983 Supported distributed kernel in 1D convolution](https://github.com/helmholtz-analytics/heat/pull/983)

- [#1007 Implemented distributed 2D convolution](https://github.com/helmholtz-analytics/heat/pull/1007)

### Branches created

- [distributed-kernel-1D](https://github.com/helmholtz-analytics/heat/tree/distributed-kernel-1D)

- [feature920/distributed-2D-convolution](https://github.com/helmholtz-analytics/heat/tree/feature920/distributed-2D-convolution)

## Future Plans

- Completion of generic `_convolve_op()` and addition of scalability tests and improved memory footprint.
- Supporting statistical estimators in Heat.
  - Development of memory-distributed `gaussian_kde`.
  - Research on conversion of scikit-learn based KDE to a parallel version of it.
- Contribute to Heat for implementing, improving, and debugging code.


## Conclusion

The project's planned objectives were achieved with success. Currently, I'm in the testing phase, and evaluating my code on user's data. I encountered multiple challenges while working on the project, like how memory distributed algorithms work, understanding and visualising communications in MPI, but I learned how to overcome them. Over time, I learned about a variety of methods, and concepts for developing new parallel algorithms. In the future, I hope to contribute to the project by supporting parallelization of the RFI mitigation pipeline for the SKA-MPG prototype antenna and also implementing new features and improving existing ones.

It's been an amazing experience for me and I've learned a lot in the last twelve weeks that will undoubtedly help me achieve my future goals. I am extremely grateful to my mentor Claudia Comito and Heat for allowing me to work on the project and for all of their guidance and support. I would also like to thank Google for providing such a great opportunity to all open source student developers across the globe.

