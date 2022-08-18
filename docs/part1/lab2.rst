Assignment 2: Buffer Manager
==================================

**Assigned: 09/18/2021**

**Due: 10/06/2021 11:59 PM EDT**

For the second lab, you will implement the buffer manager that uses the
2Q replacement strategy. We have provided you with a set of
unimplemented functions. You will need to fill in these functions.

We **strongly recommend** that you start as early as possible on this
lab. It requires you to write a fair amount of code.


Getting started
---------------

**Start by downloading the zip file provided for this assignment from Canvas.**

Description
~~~~~~~~~~~

In this lab, you need to implement a buffer manager that uses the 2Q
replacement strategy. The implementation should take 64 bit page ids and
return pages that can be locked (shared or exclusive), read and written.
The 64 bits of a page id are divided into a segment id and a segment
page id. The most significant 16 bits are the segment id and the least
significant 48 bits are the segment page id. When a page is written to
disk, it must be stored in a file whose filename is the segment id. Your
implementation should support different page and buffer sizes and be
able to fix and unfix pages concurrently from different threads.

Implementation Details
~~~~~~~~~~~~~~~~~~~~~~

We provide you with the skeleton code required to implement the buffer
manager. Add your implementation to the files
``src/include/buffer/buffer_manager.h`` and
``src/buffer/buffer_manager.cc``. There you can find the classes
``BufferFrame`` which represents a page and ``BufferManager`` which
represents the buffer manager. All definitions for the methods that you
should implement are provided. You can add new member functions and
member variables to either class as needed.

It may be useful to use the class ``buzzdb::File`` from
``src/include/storage/file.h``. Note that only ``File.read_block()`` and
``File.write_block()`` are thread-safe. Also, using multiple ``File``
objects that refer to the same file concurrently also leads to
unexpected results. Also, to ensure thread-safety you can use
`std::mutex <https://en.cppreference.com/w/cpp/thread/mutex>`__
and
`std::shared_mutex <https://en.cppreference.com/w/cpp/thread/shared_mutex>`__.

Your implementation should pass all of tests starting with
``BufferManagerTest``. Tests whose name contains ``Multithread`` are
executed with a timeout of 30s. This is done so that a deadlock does not
block the tester.

We will also grade your implementation based on its efficiency. You
don’t have to optimize on assembly level or try to improve the runtime
by microseconds. Instead, we want you to especially take care when
holding latches. They should be held as short as possible to enable
better concurrency. Most importantly, latches should not be held while
reading from or writing to disk as this essentially blocks all other
threads for the duration of the I/O operation which can be many
milliseconds.

Build instructions:
~~~~~~~~~~~~~~~~~~~

Enter BuzzDB’s directory and run

.. code-block:: sh

   mkdir build
   cd build
   cmake -DCMAKE_BUILD_TYPE=Release ..
   make

Test Instructions:
~~~~~~~~~~~~~~~~~~

To run the entire test suite, use:

.. code-block:: sh

   ctest

ctest has a flag option to emit verbose output. Please refer to `ctest
manual <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__.

We have provided all the test cases for this lab. Gradescope will only
test your code against these test-cases.

Similar to lab1, your implementation will be checked for memory leaks.
You can check for memory leaks using valgrind.

.. code-block:: sh

   ctest -V -R buffer_manager_test_valgrind

Logistics
---------

You must submit your code (see below) as well as an one-page writeup (in
``REPORT.md``) describing your solution. In the writeup, mention 1) the
design decisions you made, and 2) the missing components in your code.
We will award partial credits based on this writeup (if you are unable
to finish the implementation before the due date or if it fails any test
cases).

Collaboration
~~~~~~~~~~~~~

This is an individual assignment. No collaboration is allowed.

Submitting your assignment
~~~~~~~~~~~~~~~~~~~~~~~~~~

You should submit your code on Gradescope. We have set up an autograder
that will test your implementation. You are allowed to make multiple
submissions and we will use the latest submission to grade your lab.

.. code-block:: sh

   bash submit.sh <name>

**Important**

Do not add additional files to the zip file, use the script above.

Grading
~~~~~~~

85% of your grade will be based on whether or not your code passes the
autograder test suite. 10% will be awarded if your implementation meets
the requirements listed in the implementation section(correctly handling
the latches). 5% is for code quality. We will award partial marks for
submissions that fail the autograder test suite (based on the writeup).
