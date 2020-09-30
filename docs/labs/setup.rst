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
	
	# Install scp plugin for copying files between host and VM
	[host] $ vagrant plugin install vagrant-scp

        # Access the VM
	[host] $ cd Desktop
        [host] $ mkdir buzzdb

        # Initialize the VM
        [host] $ vagrant init ubuntu/bionic64
	
	# Start the VM
        [host] $ vagrant up
	
	# Enter into the VM
        [host] $ vagrant ssh
	[vm] $ pwd
	[vm] $ ls
			
	# To exit the VM (at any point in time)
	[vm] $ exit
	
	# Check hostname of VM (e.g., "default")
	[host] $ vagrant status

        # Get the Vagrant SSH configuration
        [host] $ vagrant ssh-config
    
        # Copy the output of the previous command to the end of the local SSH configuration file
        [host] $ vi ~/.ssh/config
    
        # You should be able to connect to the vm via SSH (user@hostname)
        [host] $ ssh vagrant@default

Bump up the amount of DRAM assigned to the VM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The default setting is 1~GB. You should uncomment and modify the generated `Vagrantfile` within the `buzzdb` folder to increase memory size (to 2~GB or higher if possible).
     
.. code-block:: ruby

    config.vm.provider "virtualbox" do |vb|
        vb.memory = "2048"
    end 

Installing Packages
~~~~~~~~~~~~~~~~~~~

Once you have Ubuntu OS up and running, install all required packages for this course:

.. code-block:: sh

    # Install packages
    [host] $ sudo apt-get -y update
    [host] $ sudo apt-get -y install build-essential 
    [host] $ sudo apt-get -y install unzip git cmake llvm valgrind clang clang-tidy clang-format googletest zlib1g-dev libgflags-dev libbenchmark-dev
    [host] $ cd /usr/src/googletest; sudo mkdir build; cd build; sudo cmake ..; sudo make; sudo cp googlemock/*.a googlemock/gtest/*.a /usr/lib; cd /vagrant/;

    # Install zsh + oh-my-zsh | for automated command completion and reverse search through command history
    # Reference: https://hackernoon.com/oh-my-zsh-made-for-cli-lovers-bea538d42ec1
    [host] $ sudo apt-get -y zsh
    [host] $ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

Installing Editor
~~~~~~~~~~~~~~~~~

We recommend using `VSCode <https://code.visualstudio.com/>`_ for the programming assignments.

#. Here's a guide for `Getting started with VSCode <https://code.visualstudio.com/docs>`_. 

#. Install these two extensions in VSCode: 
    - `C++ <https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools>`_
    - `Remote SSH <https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh>`_
    
#. You can now connect to the remote host (i.e., the VM) using the `Remote SSH extension <https://code.visualstudio.com/docs/remote/ssh#_connect-to-a-remote-host>`__

#. VSCode comes with a built-in terminal. 
    
--------------

Questions or comments regarding cs4420?
Send an e-mail to `arulraj@gatech.edu <mailto:arulraj@gatech.edu>`__.
