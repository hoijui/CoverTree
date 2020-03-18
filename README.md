# Tree Based Nearest Neighbor Search with Guarantees

We present parallel implementations of two tree based nearest neighbor search data structures:
SG-Tree and Cover Tree.

## Cover Tree

The cover tree data structure was originally presented in and improved in:

1. Alina Beygelzimer, Sham Kakade, and John Langford. "Cover trees for nearest neighbor."
   Proceedings of the 23rd international conference on Machine learning. ACM, 2006.
2. Mike Izbicki and Christian Shelton. "Faster cover trees."
   Proceedings of the 32nd International Conference on Machine Learning (ICML-15). 2015.

## SG-Tree

SG-Tree is a new data structure for exact nearest neighbor search
inspired from Cover Tree and its improvement,
which has been used in the [TerraPattern](http://www.terrapattern.com/) project.
At a high level, SG-Tree tries to create a hierarchical tree
where each node performs a "coarse" clustering.
The centers of these "clusters" become the children
and subsequent insertions are recursively performed on these children.
When performing the NN query,
we prune out solutions based on a subset of the dimensions that are being queried.
This is particularly useful when trying to find the nearest neighbor
in highly clustered subset of the data,
e.g. when the data comes from a recursive mixture of Gaussians
or more generally time marginalized coalescent process.
The effect of these two optimizations is,
that our data structure is extremely simple, highly parallelizable
and is comparable in performance to existing NN implementations on many data-sets.
 
Under active development

### New: Moving to Python3

### New: Python wrappers added

Just use `python setup.py install` and then in python you can `import nntree`.
The python API details are provided in `API.pdf`.
If you do not have root privileges,
install with `python setup.py install --user`,
and make sure to have the folder in path.

## Organisation

- `src` All the native source-code, within respective folders
- `covertree` python wrapper source code
- `lib` Native dependencies
- `data` contains script(s) to generate synthetic data
- `windows` Visual Studio project files
- `build` will be created to hold build artifacts
- `dist` will be created to hold the executables

## Requirements

One of these C++ compilers (supporting c++14 features):

- gcc v5.0 or later
- Intel&reg; C++ Compiler 2017 or later
- LLVM/CLang v3.4 or later

## How to use

We will show how to run our Cover Tree on a single machine using a synthetic dataset.

1. Compile by hitting make

- using GCC v5.0+:

   ```bash
make all
   ```

- or using Intel&reg; C++ Compiler 2017+:

   ```bash
make intel
   ```

- or using Intel&reg; C++ Compiler 2017+ with cross-file optimization (ipo):
   
   ```bash
make inteltogether
   ```

- or using LLVM/CLang 3.4+:

   ```bash
make llvm
   ```

For this to work under linux,
you would probably have to install at least these packages
(in version 3.4 or later):
`clang libc++-dev`

2. Generate synthetic dataset

   ```bash
python data/generateData.py
   ```

3. Run Cover Tree

   ```bash
dist/cover_tree data/train_100d_1000k_1000.dat data/test_100d_1000k_10.dat
   ```

- Also, you can selectively compile individual modules by specifying

   ```bash
make <module-name>
   ```

- or clean individually by

   ```bash
make clean-<module-name>
   ```

## Performance

Based on our evaluation,
the implementation is easily scalable and efficient.
For example on Amazon EC2 c4.8xlarge,
we could insert more than 1 million vectors of 1000 dimensions in Euclidean space
with L2 norm under 250 seconds.
During query time,
we can process > 300 queries per second per core.

## Troubleshooting

If the build fails and throws an error like "instruction not found",
then most probably the system does not support the AVX2 instruction sets.
To solve this issue,
please change `march=core-avx2`to `march=corei7` in `setup.py` and `src/cover_tree/makefile`.
