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

#. Download and install `Vagrant <http://www.vagrantup.com/downloads.html>`_

#. Add the Ubuntu Virtual Machine (VM)

    .. code-block:: sh
        # Open Terminal (e.g., `Terminal` in Mac)
	
        # Download the VM
        [host] $ vagrant box add ubuntu/bionic64

        # Access the VM
	[host] $ cd Desktop
        [host] $ mkdir buzzdb

        # Initialize the VM
        [host] $ vagrant init ubuntu/bionic64
	
	# Start the VM
        [host] $ vagrant up
	
	# Enter into the VM
        [host] $ vagrant ssh
	[host] $ cd /vagrant/
	[host] $ ls
	
	# To exit the VM (at any point in time)
	[host] $ exit
	
Bump up the amount of DRAM assigned to the VM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The default setting is 1~GB. You should uncomment and modify the generated `Vagrantfile` within the `buzzdb` folder to increase memory size (to 2~GB or higher if possible).
     
.. code-block:: ruby

    config.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
    end 

Installing Packages
~~~~~~~~~~~~~~~~~~~

Once you have Ubuntu OS up and running, install all required packages for 
this course:

.. code-block:: sh

    # Install packages
    [host] $ sudo apt-get -y update
    [host] $ sudo apt-get -y install build-essential 
    [host] $ sudo apt-get -y install unzip git cmake llvm valgrind clang clang-tidy clang-format googletest zlib1g-dev libgflags-dev libbenchmark-dev
    [host] $ cd /usr/src/googletest; sudo mkdir build; cd build; sudo cmake ..; sudo make; sudo cp googlemock/*.a googlemock/gtest/*.a /usr/lib; cd /vagrant/;

    # Download lab zip file from Canvas and put it in the buzzdb folder
    [host] $ mv lab1.zip buzzdb
    [host] $ cd buzzdb
    [host] $ unzip lab1.zip
    [host] $ cd lab1

[Optional] 

.. code-block:: sh

    # Install zsh + oh-my-zsh | For automated command completions and reverse search through command history
    # Reference: https://hackernoon.com/oh-my-zsh-made-for-cli-lovers-bea538d42ec1
    [host] $ sudo apt-get -y zsh
    [host] $ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

Tool Guide
----------

Familiarity with these powerful tools is crucial for productive development and debugging. Don't take our word for it, though. Read the relevant manuals.

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

See the `GDB manual <http://sourceware.org/gdb/current/onlinedocs/gdb/>`__ for a detailed guide. Here are some particularly useful GDB commands for this course.

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

Valgrind is useful for detecting memory leaks. Here's a particularly useful valgrind command for this course.

valgrind --leak-check=full --show-leak-kinds=all --track-origins=yes ./build/test/foo_test
    Usese memcheck for detecting common memory errors.
    
--------------

Questions or comments regarding cs4420?
Send an e-mail to `arulraj@gatech.edu <mailto:arulraj@gatech.edu>`__.
