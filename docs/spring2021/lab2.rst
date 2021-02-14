.. role:: bash(code)
   :language: bash

.. include:: ../.colors.rst


CS 8803 Lab 2: CPP - Tutorial
==============================

**Due: 3/1/2021 11:59 PM EST**

Project Description
-------------------
The goal of the lab is to implement ``Logging`` and ``Recovery`` mechanism in buzzdb. You need to implement a write ahead logging (WAL) under No-Force/Steal policy. You need to log every page-level write operation and trasacation command.

Program Specification
-----------------------
We have provided you with ``LogManager``stub that contains APIs that you need to implement.
You should not modify the signature of the pre-defined functions. You should also not modify any predefined member variables.
You are however allowed to add new helper private functions/member variables in order to correctly realize the functionality.

The lab can be divided into two tasks: 
Task #1: Logging

To achieve the goal of atomicity and durability, the database system must output to stable storage information describing the modifications made by any transaction, this information can help us ensure that all modifications performed by committed transactions are reflected in the database (perhaps during the course of recovery actions after a crash). It can also help us ensure that no modifications made by an aborted or crashed transaction persist in the database. The most widely used structure for recording database modifications is the log. The log is a sequence of log records, recording all the update activities in the database. 
The buzzbd database system has a global ``LogManager`` object which is responsible logging information. The `HeapSegment` will explicitly call the `log_update` API before making any update operations. 

API Requirements and Hints
----------------------------

.. code-block:: c++
   get_total_log_records()

Returns the count of total number of log records in the log file 

To successfully pass all the test cases, you need to add required implementation in the ``log_manager.cc`` and ``log_manager.h``.
You are free to choose the logging 
We have added detailed comments for what needs to be done in each function.


Prerequisites
~~~~~~~~~~~~~~

You need to follow the instructions mentioned in the `setup <setup.html>`__ document. 

Download the handout shared via Canvas. You need to copy the handout to the VM (skip this if you are not using VM).

.. code-block:: bash

   # Copy the handout to the VM
   [host] $ vagrant scp [path_to_handout] default:buzzdb-logging_recovery.zip
   [host] $ cd buzzdb
   # Get into VM 
   [host] $ vagrant ssh
   # unzip the handout 
   [vm] $ unzip buzzdb-logging_recovery.zip


 
Instructions for execution
~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. highlight:: bash
   

.. code-block:: bash
   
   [vm] $ cd buzzdb-logging_recovery
   [vm] $ mkdir build 
   [vm] $ cd build
   [vm] $ cmake -DCMAKE_BUILD_TYPE=Release ..
   [vm] $ make
   [vm] $ ctest

We treat compiler warnings as errors. Your project will fail to build if there are any compiler warnings.


General Instructions
----------------------

Your program must be written only in C++. Your coding style should have well-defined classes and clean interfaces. The code should be well-documented. Each file should start with a header describing the purpose of the file and should also contain your name, GT UserID, and GT email address.

Testing for correctness involves more than just seeing if a few test cases produce the correct output. There are certain types of errors (memory errors and memory leaks) that usually surface after the system has been running for a longer period of time. 
You should use `valgrind` to isolate such errors. Command to run `valgrind` can be found `here <tools.html#valgrind>`__.

You will get a listing of memory errors in your program. If you have programmed in Java you should keep in mind that C++ does not have automatic garbage collection, so each new must ultimately be matched by a corresponding delete. Otherwise all the memory in the system might be used up. Valgrind can be used to detect such memory leaks as well. More information about valgrind can be found at: http://www.valgrind.org/docs/manual/index.html.


.. _submit:

Submitting Your Assignment
---------------------------

.. code-block:: bash

   bash submit.sh <name>

You will be submitting your assignment on Gradescope. You are expected to run :file:`submit.sh` and submit the generated zip to the autograder.

You can use :file:`REPORT.md` to describe the following design and program criteria (**optional**). In case you don't complete all the testcases, we will award you partial points based on the report.

1. Explain your choice of the data structure that you implemented. Did you consider any other data structures besides the one that you implemented? How did you arrive at your final choice of the data structure?

2. What is the best, average, and worst case complexity of your implementation of the locate command in terms of the number of words in the file that you are querying? (you need to provide all three - best, average, and worst-case analysis). For the complexity, I am only interested in the big-Oh analysis.

3. What is the average case space complexity of your data structure in terms of the number of words in the input file? In other words, using the big-Oh notation what is the expected average size of your data structure in terms of the number of words.


Grading
---------

This assignment is worth 5% of your grade. The maximum score on this assignment is 100.