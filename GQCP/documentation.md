# Documentation


## Introduction

GQCP naturally uses an object-oriented programming style in order to convey quantum chemical concepts into source code.
Since second quantization is a modern approach for wave function-based quantum chemistry, you will find many of the interfaces intuitive if you are familiar with it.


## General structure of the C++ library

In this section, we will go over the main aspects of the C++ library.
It is meant as an introduction to the source code for interested users, but mainly for C++ developers that are interested in how GQCP works under the hood.
By reading this section, we assume that you are familiar with C++.

Since the GQCP source code uses concepts related to second quantization, we should start by examining the core objects `SQOneElectronOperator` and `SQTwoElectronOperator`: the prefix 'SQ' means 'second-quantized', we have used it in order to somewhat abbreviate the class names. 
`SQOperator`, which is not a real class but is used to refer to either `SQOneElectronOperator` or `SQTwoElectronOperator`, encapsulates the integrals/parameters that are present in the corresponding second-quantized expression, so there must be a way to create an `SQOperator` from its integrals/parameters.
Let us go through a small snippet to create an `SQOneElectronOperator`. 

```cpp
#include <gqcp.hpp>

// Initialize two matrices and convert them into operators
GQCP::QCMatrix<double> M (2);
M1 << 1.0, 2.0,
      3.0, 4.0;

const GQCP::ScalarSQOneElectronOperator<double> op ({M});
```

If the syntax for the matrix-related objects feels familiar for other C++ developers, it might be because we use the amazing linear algebra library [Eigen](http://eigen.tuxfamily.org) for representing vectors, matrices and tensors and performing BLAS/LAPACK operations.
In this example, we first create a matrix representation using Eigen's syntax and afterwards create an `SQOneElectronOperator` from it.
