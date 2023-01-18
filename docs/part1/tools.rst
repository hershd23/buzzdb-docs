Tool Guide
==========

Familiarity with these powerful tools is crucial for productive development and debugging.

      - `CMake <#cmake>`__
      - `GDB <#gdb>`__
      - `Valgrind <#valgrind>`__


CMake
~~~~~

`CMake <https://cmake.org/>`_ is a cross-platform tool for managing the build process of software systems.

cmake -DCMAKE_BUILD_TYPE=Debug ..
    Generate a Makefile in Debug mode
cmake -DCMAKE_BUILD_TYPE=Release ..
    Generate a Makefile in Release mode
make
    Build database system. 
make check
    Run all of the test cases.
./build/test/unit/foo_test
    Directly run a particular unit test
make clean 
    Clean the directory.

GDB
~~~

.. code-block:: bash

    # To set up GDB: (Run this from the build folder) 
    $ cmake -DCMAKE_BUILD_TYPE=Debug ..
    $ make
    $ gdb ./test/buffer_manager_test 
    
    # Example uses 
    
    # If you want ot test a specific test case: 
    $ (gdb) run --gtest_filter=BufferManagerTest.InsertTest
    
    # If you want to see the value of the variable, you can use the following command.
    $ (gdb) print buffer_pool_manager_
    
    # If you want to see the backtrace of the crash, you can use the following command.
    $ (gdb) bt
    
     
`GDB <https://www.gnu.org/software/gdb/>`_ is a widely used debugger. Here are some useful GDB commands for this course.

Ctrl-c
    Halt the program and break in to GDB at the current instruction. 
    If the program spawns multiple threads, this command halts all of them.
c (or continue)
    Continue execution until the next breakpoint or ``Ctrl-c``.
si (or stepi)
    Execute one machine instruction.
b function or b file\:line (or breakpoint)
    Set a breakpoint at the given function or line.
set print pretty
    Enable pretty-printing of arrays and structs.
thread *n*
    GDB focuses on one thread at a time. This command switches the context 
    to thread number *n*, numbered from zero.
info threads
    List all threads (i.e., CPUs), including their state (active or
    halted) and what function they are in.
info sources
    Lists source files mentioned in the loaded symbols
    
See the `GDB manual <https://www.sourceware.org/gdb/documentation/>`__ for a detailed guide.     

Valgrind
~~~~~~~~

`Valgrind <https://valgrind.org/docs/manual/mc-manual.html>`_ is useful for detecting memory leaks. Here's an useful valgrind command for this course.

valgrind --leak-check=full --show-leak-kinds=all --track-origins=yes ./test/<executable>
    Uses memcheck for detecting common bugs related to memory management.
