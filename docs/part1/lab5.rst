Assignment 5: Slotted Pages
============================

In this lab, you will be implementing support for slotted pages.

Getting started
---------------

Description
~~~~~~~~~~~

Implement the *schema segment* and the *slotted pages*. In the schema
segment, you should store the segment ids of the free-space inventory
and the slotted page segment as well as the total number of existing
slotted pages and the schema string. The slotted page segment should
store slotted pages as discussed in the lecture. Your implementation
should support allocation, reading, writing, and resizing of records in
a page. You should also implement TID redirects and use a free-space
inventory to find suitable pages more efficiently.

``src/include/catalog/schema.h`` contains a simplified schema that you
should be able to serialize and deserialize into the schema segment. For
this purpose we also added the library *rapidjson*
(``vendor/rapidjson.cmake``) that you could use.

Important files for this assignment:

-  ``src/include/catalog/schema.h``

-  ``src/include/storage/segment.h``

-  ``src/include/storage/slotted_page.h``

-  ``src/storage/fsi_segment.cc``

-  ``src/catalog/schema.cc``

-  ``src/catalog/schema_segment.cc``

-  ``src/storage/slotted_page.cc``

-  ``src/storage/sp_segment.cc``

Implementation Details
~~~~~~~~~~~~~~~~~~~~~~

Add your implementation to the files ``src/include/catalog/schema.h``,
``src/catalog/schema.cc``, ``src/storage/sp_segment.cc`` and
``src/include/storage/segment.h``. All definitions for the methods that
you should implement are provided. You can add new member functions and
member variables to all classes as needed.

Your implementation should pass all tests starting with ``SegmentTest``.
We will of course check your code for correctness. Passing the tests is
a requirement but does not automatically mean that you will get full
points.

Detailed Instructions
---------------------

Start the project by implementing ``SchemaSegment::read`` and
``SchemaSegment::write`` functions.

The schema segment should be structured as:

-  The segment id of the slotted pages segment

-  The segment id of the free-space inventory segment

-  The size of the slotted pages segment (in #pages)

-  The length of the serialized schema (in #bytes)

-  The serialized schema

Use `rapidjson <http://rapidjson.org/md_doc_schema.html>`__ to serialize
and deserialize.
``rapidjson::Document, rapidjson::Writer and rapidjson::Value`` are the
key APIs to look for in the documentation. Note that the serialized
schema *could* be larger than 1 page.

Once all the test cases with prefix schema pass, proceed to implement
the
``SPSegment::read, SPSegment::write, SPSegment::resize and SPSegment::allocate.``
High level description of each function is provided in ``segment.h``. To
simplify this lab, we have provided implementation of
``free-space inventory`` and ``slotted pages``. Before implementing
``SPSegment``, go through the available functions in ``FSISegment`` and
``SlottedPage``.

Build instructions:
~~~~~~~~~~~~~~~~~~~

Enter BuzzDB's directory and run

::

    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make

Test Instructions:
~~~~~~~~~~~~~~~~~~

To run the entire test suite, use:

::

    ctest

ctest has a flag option to emit verbose output. Please refer to `ctest
manual <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__.

We have provided all the test cases for this lab. Gradescope will only
test your code against these test-cases.

Similar to other labs, your implementation will be checked for memory
leaks. You can check for memory leaks using valgrind.

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

::

    bash submit.sh <name>

***Important*** Do not add additional files to the zip file, use the
script above.

Grading
~~~~~~~

95% of your grade will be based on whether or not your code passes the
autograder test suite. 5% is for code quality. We will award partial
marks for submissions that fail the autograder test suite (based on the
writeup).
