Setup Development Environment
=============================

Before hacking on the labs, you will need to setup the development environment 
described below.

Our labs are designed to run on 
`Ubuntu 18.04 <https://en.wikipedia.org/wiki/Ubuntu>`__ 
(Linux Distribution).
We recommend that you install this specific version of the operating system 
(**18.04**).
Otherwise, you may face issues with running the shell scripts in the labs.

This page contains information on how you could download and install the 
Ubuntu development environment. We will assume familiarity with Unix commands 
throughout.

Ubuntu 18.04 LTS
----------------
If you are already using Ubuntu 18.04 on your laptop, then you can skip these
steps and directly proceed to `Installing Packages <#installing-packages>`__. 
If that is not the case, then you should install a virtual machine.

Install Virtual Machine
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Our recommended way of setting up your virtual machine is to use 
`Vagrant <https://www.vagrantup.com/intro>`__  along with 
`VirtualBox <https://www.virtualbox.org/manual/ch01.html#virt-why-useful>`__.
Vagrant enables users to create and configure lightweight, reproducible, and 
portable development environments. VirtualBox is a general-purpose full 
virtualizer for x86 hardware.

#. Download and install Virtualbox

    - (Windows) Turn off Hyper-V in "Turn Windows features on or off"
    - Download and install `VirtualBox 
      <https://www.virtualbox.org/wiki/Download_Old_Builds_6_0>`__ 
        - Use **6.0.24** version.
        - Vagrant does currently not work with version *6.1* of VirtualBox.

#. Download and install `Vagrant <http://www.vagrantup.com/downloads.html>`__
		- Use **2.2.6** version.

#. Add the Ubuntu Virtual Machine (VM)

    .. code-block:: sh

        # Add Ubuntu VM
        [host] $ vagrant box add ubuntu/bionic64

        # Access the VM
        [host] $ mkdir buzzdb
        [host] $ cd buzzdb
        [host] $ vagrant init ubuntu/bionic64
        [host] $ vagrant up
        [host] $ vagrant ssh

Bump up the amount of DRAM assigned to the VM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The default setting is 1GB. You should uncomment and modify the `Vagrantfile` 
as follows to increase the memory size.
     
.. code-block:: ruby

    config.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
    end 

Installing Packages
~~~~~~~~~~~~~~~~~~~

Once you have Ubuntu guest OS up and running, install all required packages for 
this course:

.. code-block:: sh

    # install git
    $ sudo apt-get install git

    # install other packages
    $ sudo sh packages.sh
    
Tool Guide
----------

Familiarity with your environment is crucial for productive development and 
debugging. Don't take our word for it, though. Read the relevant manuals. 
These are powerful tools that are worth learning more about.

.. note::

    Reference:
      - `CMake <#cmake>`__
      - `GDB <#gdb>`__
      - `Valgrind <#valgrind>`__

Reference
---------

CMake
~~~~~

Our Makefile includes a number of targets to test and run buzzdb.

cmake -DCMAKE_BUILD_TYPE=Debug ..
    Generate a Makefile in Debug mode
cmake -DCMAKE_BUILD_TYPE=Release ..
    Generate a Makefile in Release mode
make
    Build database system. 
make check
    Run all of the test cases.
./build/test/foo_test
	Directly run a particular unit test
make clean 
    Clean the directory.

GDB
~~~

See the `GDB
manual <http://sourceware.org/gdb/current/onlinedocs/gdb/>`__ for a detailed
guide. Here are some particularly useful GDB commands for this course.

Ctrl-c
    Halt the program and break in to GDB at the current instruction. 
    If the program spawns multiple threads, this command halts all of them.
c (or continue)
    Continue execution until the next breakpoint or ``Ctrl-c``.
si (or stepi)
    Execute one machine instruction.
b function or b file\:line (or breakpoint)
    Set a breakpoint at the given function or line.
b \*\ *addr* (or breakpoint)
    Set a breakpoint at the EIP *addr*.
set print pretty
    Enable pretty-printing of arrays and structs.
thread *n*
    GDB focuses on one thread at a time. This command switches the context 
    to thread number *n*, numbered from zero.
info threads
    List all threads (i.e., CPUs), including their state (active or
    halted) and what function they are in.

Valgrind
~~~~~~~~

--------------

Questions or comments regarding cs4420?
Send an e-mail to `arulraj@gatech.edu <mailto:arulraj@gatech.edu>`__.
