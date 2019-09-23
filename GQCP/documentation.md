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


// Initialize a matrix and convert it into an operator
GQCP::QCMatrix<double> M (2);  // the matrix representation
M << 1.0, 2.0,
     3.0, 4.0;

const GQCP::ScalarSQOneElectronOperator<double> op ({M});  // the operator itself
```


If the syntax for the matrix-related objects feels familiar for other C++ developers, it might be because we use the amazing linear algebra library [Eigen](http://eigen.tuxfamily.org) for representing vectors, matrices and tensors and performing BLAS/LAPACK operations.
In this example, we first create a matrix representation using Eigen's syntax and afterwards create an `SQOneElectronOperator` from it.
`SQOneElectronOperator` is actually a class template, which has two template arguments.
The first one is the underlying type of the matrix elements/integrals/parameters, and the second is the number of components the operator has.
We are most often working with `SQOperator`s with only one component, like the kinetic energy operator, or the Coulomb repulsion operator, but occasionally, we work with electronic dipole operators as well.
You might have guessed, but `ScalarSQOperator` is just an alias, which for `SQOneElectronOperator` is written as follows:


```cpp
template <typename Scalar>
using ScalarSQOneElectronOperator = SQOneElectronOperator<Scalar, 1>;
```


In order to access the underlying integrals/parameters/matrix elements, we use the API `.parameters(component_index)` or `.allParameters()`. Building on the previous example, we can now write the following.


```cpp
#include <gqcp.hpp>


// Initialize two matrices and convert them into the two components of an operator
GQCP::QCMatrix<double> M1 (2);  // the first matrix representation
M1 << 1.0, 2.0,
      3.0, 4.0;

GQCP::QCMatrix<double> M2 = GQCP::QCMatrix<double>::Identity(2);  // the second first matrix representation


// Construct an operator with two components
const GQCP::SQOneElectronOperator<double, 2> op ({M1, 2*M1*M2});  // note that we can naturally work with multiplications of matrices thanks to QCMatrix inheriting from Eigen


const auto all_components = op.allParameters();  // return all the components
std::cout << op.parameters(1)(0,1) << std::endl;  // access the element (0,1) of the first component: this should print '2'
```

We should note that this type of access both works in a read-only and a write way.
