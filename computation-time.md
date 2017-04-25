# Computation time

The scaling of the time taken to compute 7749 features of v0.94 of _hctsa_ is shown below. The figure compares results using a single core (e.g., `TS_compute(false)`) to results using a 16-core machine, enabling parallelization (e.g., `TS_compute(true)`).

![](/assets/computeScaling.png)