Development Environment
=======================

You will need to setup a development environment to work on the programming assignments.

Our labs are designed to run on the `Ubuntu 22.04 Operating System (OS) <https://en.wikipedia.org/wiki/Ubuntu>`__. Ubuntu is a Linux distribution. We recommend that you install this OS either natively or using Docker by following the instructions below. Otherwise, you could face issues while running the shell scripts in the programming assignments.

This page contains information on setting up the Ubuntu OS development environment. We will assume familiarity with the following concepts throughout:

- `Linux commands <https://ubuntu.com/tutorials/command-line-for-beginners#1-overview>`__
- `Docker <https://docs.docker.com/>`__
- `cmake <https://buzzdb-docs.readthedocs.io/resources/tools.html#cmake>`__
- `make <https://cplusplus.com/articles/jTbCpfjN/>`__
- `ctest <https://cmake.org/cmake/help/latest/manual/ctest.1.html>`__

If you are unfamiliar with any of the above, we highly recommend going through the corresponding links.

We also recommend going through the `"Additional Resources" <https://buzzdb-docs.readthedocs.io/resources/index.html>`__.

.. code-block:: sh

	NOTE: While we will always try to keep the documentation up-to-date. If you ever get stuck on some topic or
	      would like additional context, feel free to use the good old reliable ChatGPT =)

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

#. Next, in order to open up the folder within the Ubuntu container, run the "**Dev Containers: Open Folder in Container...**" command from VS code Command Palette (F1)
   or quick actions Status bar item and select the lab1 folder. We have already provided a `devcontainer.json` to assist as a starting point for the container. Once
   done, VS Code will reload and start building the dev container. More information on this step can be found `here <https://code.visualstudio.com/docs/remote/containers#_quick-start-open-an-existing-folder-in-a-container>`__.

WSL Ubuntu
-----------

If you are using Windows you can also use `Windows Subsystem for linux <https://learn.microsoft.com/en-us/windows/wsl/install>`__ along with `VSCode <https://code.visualstudio.com/>`__. 

- WSL allows developers to use linux applications directly on windows, without have to switch between different Operating System.

#. We will first setup Visual Code. `Install it first <https://code.visualstudio.com/download>`__. This page contains some `useful resources <https://code.visualstudio.com/docs>`__.

#. Install wsl on windows, along with a linux distribution (Ubuntu is recommended).

.. code-block:: sh

	# install wsl
	wsl --install

3. After installation you can open your wsl's terminal by running `wsl.exe` command

.. code-block:: sh

	# In a new powershell window
	wsl.exe

	# Try out a few commands
	whoami

	# Change directory to your lab folder
	cd /mnt/drive/path/to/folder

	# Open the folder in VSCode
	code .


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
