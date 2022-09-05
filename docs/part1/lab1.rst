Assignment 1: External Sort
=======================================

**Assigned: 08/25/2022**

**Due: 09/08/2022 11:59 PM EDT**

In the programming assignments for this course, you will be implementing a the components of a toy database management system codenamed BuzzDB. All the labs are written in `C++ <https://en.cppreference.com/w/>`__.

In the first assignment, you will implement the `external sort algorithm <https://en.wikipedia.org/wiki/External_sorting>`__. This algorithm allows us to sort a large dataset that does not fit into the main memory of the server.

We have provided you with a set of unimplemented functions. You will need to fill in these functions. We will grade your code by running a set of system tests written using `Google Test <https://github.com/google/googletest>`__. We have provided a set of unit tests that you may find useful in verifying that your code works.

.. warning::
  We **strongly recommend** that you start as early as possible on this lab as it involves a fair amount of programming and you will also need to learn about C++.

Environment Setup
----------------

**Start by downloading the zip file provided for this assignment from Canvas.**

Setup the development environment `using the instructions given here <https://buzzdb-docs.readthedocs.io/part1/setup.html>`__.

Getting started 
----------------

Description
~~~~~~~~~~~

In this lab, you need to implement the external sort algorithm. Specifically, you will be provided with a memory constraint that your sorting program should not exceed. You will need to divide the input into K individual runs each smaller than the given memory constraint, and then sort the run in memory. You should then implement a K-way merge algorithm to merge the K sorted runs that each fit in memory. 

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

  ctest --verbose -R external_sort_test_valgrind
 
To run the entire test suite, use:

.. code-block:: sh

  ctest --verbose

Remove the `verbose` flag to only get summary information instead of detailed test output that is normally suppressed. Please refer to `ctest manual <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__.

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

  bash submit.sh <last-name-in-lowercase-letters-without-spaces>

.. warning:: **WARNING** Do not add additional files to the zip file, use the ``submit.sh`` script.  

Grading 
-------

Grade will be based on whether or not your code passes the autograder test suite. These tests will be a superset of the tests we have provided. We will award partial marks for submissions that fail the autograder test suite (based on the writeup).


Detailed Instructions
---------------------

1. Configure cmake to generate Makefiles in `debug` mode. Use a debugger like GDB.

.. code-block:: sh

  cmake -DCMAKE_BUILD_TYPE=Debug .. 
  make

2. Here are two techniques for reading and writing data -- using traditional pointers and using smart pointers.

.. code-block:: c++

    std::unique_ptr<File> chunk_file;
    chunk_file = std::move(File::make_temporary_file());
    size_t num_bytes = num_values * sizeof(uint64_t);
    
    // unique_ptr -- smart pointer for automatically releasing memory 
    auto chunk = std::make_unique<uint64_t[]>(num_values);    
    input.read_block(0, num_bytes, reinterpret_cast<char *>(chunk.get()));
    chunk_file->write_block( reinterpret_cast<char *>(chunk.get()), 0, num_bytes);
    
    // traditional pointer -- creating chunk in heap memory
    uint64_t *chunk2 = new uint64_t[num_values];
    input.read_block(0, num_bytes, (char *)(chunk2));    
    chunk_file->write_block((char *)chunk2, 0, num_bytes);    
    // Manually deleting chunk to avoid memory leak
    delete[] chunk2;

3. Run ``cmake`` from the ``build`` sub-directory that you created. ``..`` refers to the parent directory (i.e., the lab1-handout folder) containing the ``CMakeFile``.

4. To run ``valgrind`` during development to avoid memory leaks, use these commands:

.. code-block:: sh

  ctest --verbose -R external_sort_test_valgrind
  
Here's a `helpful explanation <https://stackoverflow.com/a/44989219>`_ to use ``valgrind`` for debugging memory leaks.

5. Do not forget to resize the temporary file as needed.

.. code-block:: c++
  
  temp_file->resize(input.size());

6. Another code snippet for parsing the read data:

.. code-block:: c++

  struct element {
    uint64_t value;
    size_t chunk_id = -1;
  };

  uint64_t value_buffer;

  chunk_file_registry[e.chunk_id]->read_block(read_offset, sizeof(uint64_t), reinterpret_cast<char *>(&value_buffer));

  struct element e1 = {.value = value_buffer, .chunk_id = e.chunk_id}

