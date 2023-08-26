Development Environment
=======================

You will need to setup a development environment to work on the programming assignments.

Our labs are designed to run on the `Ubuntu 22.04 Operating System (OS) <https://en.wikipedia.org/wiki/Ubuntu>`__. Ubuntu is a Linux distribution. We recommend that you install this OS either natively or using Docker by following the instructions below. Otherwise, you could face issues while running the shell scripts in the programming assignments.

This page contains information on setting up the Ubuntu OS development environment. We will assume familiarity with `Linux commands <https://ubuntu.com/tutorials/command-line-for-beginners#1-overview>`__ throughout.

Native Ubuntu OS
----------------

If you already have Ubuntu OS or another Linux distribution installed on your laptop, then install the required packages listed in the Dockerfile provided in the zip file.

.. code-block:: sh

    # Install packages
    [ubuntu] $ sudo apt-get -y update && apt-get -y install build-essential \
           && apt-get -y install zip unzip git cmake valgrind clang clang-tidy clang-format googletest zlib1g-dev libgflags-dev libbenchmark-dev libgtest-dev zsh curl git-all

    # Install zsh + oh-my-zsh 
    # Reference: https://hackernoon.com/oh-my-zsh-made-for-cli-lovers-bea538d42ec1
    [ubuntu] $ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    
Proceed to the `Building BuzzDB Labs <#building-buzzdb-labs>`__ section.

Ubuntu OS via Docker
--------------------

Our recommended way of setting up the Ubuntu development environment is to use `Docker <https://www.docker.com/>`__  along with  `VSCode <https://code.visualstudio.com/>`__. 

- Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called container. In our case, the software is Ubuntu OS. We are going to run Ubuntu OS in a container on your host OS (Mac/Windows).
- Visual Studio Code is a lightweight but powerful source code editor which runs on your desktop and is available for Windows, macOS and Linux.

#. First, download and `install Docker <https://docs.docker.com/get-started/#download-and-install-docker>`__. Dowload the appropriate file based on your host OS (Mac/Windows). Make sure Docker is running. 

#. Next, follow the following instructions to verify if the docker container works on your system. 

.. code-block:: sh

	# Unzip the compressed folder
	[host] $ unzip lab1.zip

	# Enter the lab folder
	[host] $ cd lab1
	
	# Build the docker image using the Dockerfile provided in the folder
	[host] $ docker build -t buzzdb .
	
	# That's it. You can now run a terminal within the container (Ubuntu OS)
	[host] $ docker run -it buzzdb zsh
	
	# Try out some Linux commands to gain familiarity with the container
	[ubuntu] $ whoami
	[ubuntu] $ pwd 
	
#. We will next setup Visual Code. `Install it first <https://code.visualstudio.com/download>`__. This page contains some `useful resources <https://code.visualstudio.com/docs>`__.

#. Install the following Visual Code extensions to work with C++ and Remote Containers.

	* `C++ Extension Pack <https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack>`__
	* `Remote Containers <https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers>`__

#. Next, `use the following technique to open the lab1 folder within the Ubuntu container <https://code.visualstudio.com/docs/remote/containers#_quick-start-open-an-existing-folder-in-a-container>`__. We have provided a `devcontainer.json` to assist with this step. The VScode should automatically launch a new container.

Building BuzzDB Labs
--------------------

To build the buzzdb lab, run the following commands within the terminal on the Ubuntu OS.

.. code-block:: sh

        # Enter the lab folder
	[ubuntu] $ cd lab1

	# Create the data directory.
	[ubuntu] $ mkdir test/unit/data
	
	# Make a build directory
	[ubuntu] $ mkdir build
	[ubuntu] $ cd build
	
	# Generate Makefiles using cmake
	[ubuntu] $ cmake -DCMAKE_BUILD_TYPE=Debug  ..
	
	# Build the binaries using the generated Makefiles
	[ubuntu] $ make

That's it. You should now be able to work on the assignments.

--------------

Questions or comments regarding the course?
Send an e-mail to `arulraj@gatech.edu <mailto:arulraj@gatech.edu>`__.
