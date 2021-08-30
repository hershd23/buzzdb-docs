Programming Assignment 1: External Sort
=======================================

**Assigned: 08/30/2021**

**Due: 09/13/2021 11:59 PM EDT**

In the programming assignments for this course, you will be implementing a the components of a toy database management system codenamed BuzzDB. All the labs are written in C++17. We recommend using an IDE for the labs (e.g., `VSCode <https://code.visualstudio.com/>`__ or `CLion <https://www.jetbrains.com/clion/>`__).

In the first assignment, you will implement the `external sort algorithm <https://en.wikipedia.org/wiki/External_sorting>`__. This algorithm allows us to sort a large dataset that does not fit into the main memory of the server.

We have provided you with a set of unimplemented functions. You will need to fill in these functions. We will grade your code by running a set of system tests written using `Google Test <https://github.com/google/googletest>`__. We have provided a set of unit tests that you may find useful in verifying that your code works.

> :warning: **WARNING** :warning: We **strongly recommend** that you start as early as possible on this lab. It requires you to write a fair amount of code and to also learn about C++ features.

Environment Setup
----------------

**Start by downloading the code for this assignment from Canvas.**

We will be using a Linux-based Operating System (OS) for the programming assignments. **Make sure you use Ubuntu OS (version 18.04) for running your code.** We will be testing your code on this version of the OS. If you do not have native access to this OS, you should create a virtual machine (VM) instance on `VirtualBox <https://www.virtualbox.org/wiki/Downloads>`__. Follow the instructions given `here <https://buzzdb-docs.readthedocs.io/en/latest/labs/setup.html>`__ to set up the VM and install the following software dependencies:

.. code-block:: sh

  apt-get install -yqq cmake
  apt-get install -yqq llvm
  apt-get install -yqq clang-tidy
  apt-get install -yqq clang
  apt-get install -yqq clang-format
  apt-get install -yqq valgrind
  apt-get install -yqq libgl-dev libglu-dev libglib2.0-dev libsm-dev libxrender-dev libfontconfig1-dev libxext-dev


Getting started 
----------------

Description
~~~~~~~~~~~

In this lab, you need to implement the external sort algorithm. Specifically, you will be provided with a memory constraint that your sorting program should not exceed. You will need to divide the input into K individual runs each smaller than the given memory constraint, and then sort the run in memory. You should then implement a K-way merge algorithm to merge the K sorted runs that each fit in memory. 

Be careful to handle special cases in which a simple ``sort -> K-way merge`` might not be sufficient (i.e., when K is too large to fit in memory). Note that the number of partitions (k) depends on the file size and memory size -- k = f/m. But, the number of elements in each partition (p) solely depends on the memory size, p = m/ (size of one element).

Implementation Details
~~~~~~~~~~~~~~~~~~~~~~

We provide you with the skeleton code required to implement the algorithm. You only need to fill in the ``external_sort()`` function in ``src/external_sort/external_sort.cc`` file. You are allowed to add helper functions in this file. Do not change the signature of the ``external_sort()`` function. It should take an input file that contains unsigned 64 bit integers, the number of values in the input file, an output file, and a memory constraint value (in bytes). The output file should contain values from the input file in ascending order. Your implementation must not use more bytes of heap memory than the number specified as the memory constraint. 

You should use the ``File`` API provided in ``src/include/storage/file.h`` to handle disk I/O, and to create temporary files for individual runs. You should skim through ``src/include/storage/file.h`` to understand the file API. **You are required to create a new temporary file for each individual run.** 

You are allowed to use the C++ Standard libary (std) functions for the individual runs, and for the K-way merge.  For the K-way-merge, the ``std::priority_queue`` data structure or these heap functions ``std::make_heap()``, ``std::push_heap()``, and ``std::pop_heap()`` will come in handy. 

Build instructions
~~~~~~~~~~~~~~~~~~~

Enter BuzzDB's directory and run

.. code-block:: sh

  mkdir build
  cd build
  cmake -DCMAKE_BUILD_TYPE=Release ..
  make

Test Instructions
~~~~~~~~~~~~~~~~~~

You can test your implementation by using the executable ``tool/external_sort_tool``. The tool allows you to create input file with specified number of integers, and generate the output file using the external sort algorithm (see `external_sort_tool --help` for more information).

.. code-block:: sh

  ./tool/external_sort_tool --help

You should also check your implementation against the unit tests provided in `test/unit/external_sort/external_sort_test.cc`. To run them, compile the project and then execute the ``test/external_sort_test`` binary.

.. code-block:: sh

  ./test/external_sort_test
 
Passing all the test cases is a requirement but does not automatically mean that you will get full points. We will test your implementation with more test cases.

Additionally, your implementation will be checked for memory leaks. You can check for memory leaks using valgrind.

.. code-block:: sh

  ctest -R external_sort_test_valgrind
 
To run the entire test suite, use:

.. code-block:: sh

  ctest 

ctest has a flag option to emit verbose output. Please refer to `ctest manual <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__.

Logistics 
---------

You must submit your code (see below) as well as an one-page writeup (in a file named `REPORT.md`) describing your solution. In the writeup, mention: (1) the design decisions you made, and (2) the missing components in your code. We will award partial credits based on this writeup (if you are unable to finish the implementation before the due date and/or if it fails any test cases).

Collaboration 
~~~~~~~~~~~~~

This is an individual assignment. No collaboration is allowed.

Submitting your assignment 
~~~~~~~~~~~~~~~~~~~~~~~~~~~

You should submit your code as a zip file via Gradescope. We have set up an autograder that will test your implementation. You are allowed to make multiple submissions and we will use the latest submission to grade your lab.

.. code-block:: sh

  bash submit.sh <name>

> :warning: **WARNING** Do not add additional files to the zip file, use the ``submit.sh`` script.  

Grading 
-------

95% of your grade will be based on whether or not your code passes the autograder test suite. These tests will be a superset of the tests we have provided. 5% is for code quality. We will award partial marks for submissions that fail the autograder test suite (based on the writeup).
