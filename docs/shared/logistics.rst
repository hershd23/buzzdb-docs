
Logistics
---------

You must submit your code (see below) as well as an optional one-page writeup (in
``REPORT.md``) describing your solution. In the writeup, mention: (i) the
design decisions you made, (ii) the missing components in your code,
(iii) any difficulties you encountered in completing the assignment.
We will award partial credits based on this writeup if you are unable
to finish the implementation before the due date or if you get a low score.

Submitting your assignment
~~~~~~~~~~~~~~~~~~~~~~~~~~

You should submit your code on Gradescope. We have set up an autograder
that will test your implementation. You are allowed to make multiple
submissions and we will use the latest submission to grade your lab.

.. code-block:: sh

   bash submit.sh <name>

**Important**

Do not add additional files to the zip file, use the script above.


Build instructions
~~~~~~~~~~~~~~~~~~~

Enter BuzzDB’s directory and run

.. code-block:: sh

   mkdir build
   cd build
   cmake -DCMAKE_BUILD_TYPE=Release ..
   make

We treat compiler warnings as errors. Your project will fail to build if there are any compiler warnings.

Testing Instructions
~~~~~~~~~~~~~~~~~~~~
To run the test suite in verbose mode use

.. code-block:: sh

  ctest --verbose

Remove the `verbose` flag to only get summary information instead of detailed test output that is normally suppressed. Please refer to
`ctest manual <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__.

We have provided all the test cases for this lab. Gradescope will only
test your code against these test-cases.

Your implementation will also be checked for memory leaks.
You can check for memory leaks using valgrind.

.. code-block:: sh

   ctest -V -R buffer_manager_test_valgrind

General Instructions
~~~~~~~~~~~~~~~~~~~~

Your program must be written only in C++. Your coding style should have well-defined classes and clean interfaces. The code should be well-documented. Each file should start with a header describing the purpose of the file and should also contain your name, GT UserID, and GT email address.

Testing for correctness involves more than just seeing if a few test cases produce the correct output. There are certain types of errors (memory errors and memory leaks) that usually surface after the system has been running for a longer period of time. 
You should use `valgrind` to isolate such errors. Command to run `valgrind` can be found here: https://buzzdb-docs.readthedocs.io/resources/tools.html#valgrind
You will get a listing of memory errors in your program. If you have programmed in Java you should keep in mind that C++ does not have automatic garbage collection, so each new must ultimately be matched by a corresponding delete. Otherwise all the memory in the system might be used up. Valgrind can be used to detect such memory leaks as well. More information about valgrind can be found at: http://www.valgrind.org/docs/manual/index.html.

In case you encounter any issues when running the code, you can also use debuggers to identify and fix them. Debuggers are tools that allow you to execute your code step by step, inspect the values of variables, set breakpoints, and more. Some popular debuggers for C++ are `gdb`, `lldb`, and `Visual Studio`. You can find more information about how to use debuggers here: https://buzzdb-docs.readthedocs.io/resources/tools.html#debuggers