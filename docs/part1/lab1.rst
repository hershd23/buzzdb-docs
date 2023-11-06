.. role:: bash(code)
   :language: bash

.. include:: ../.colors.rst


Assignment 1: C++ Tutorial
==========================

Description
-------------------
The goal of this assignment is to help you brush up your C++ programming skills, and exercise your skills in Data Structure and Algorithm Design. In this assignment, you are to develop a ``word locator`` program written in C++, which will allow a user to check if a specified (re)occurrence of a specified query word appears in the input text file.

CS4420 students can use the unordered map data structure from Stl. CS6422 students are required to use a Tree-based data structure. The code submissions will be checked after submission to ensure that this is followed.

.. include:: ../shared/environment_setup.rst

Implementation Details
-----------------------
For this programming assignment, you are provided with a skeleton code which you are expected to complete and submit. You are expected to fill the :command:`execute` function of :command:`CommandExecutor` class, present in these files: :file:`buzzdb/src/tutorial/tutorial.cc` and :file:`buzzdb/src/include/tutorial/tutorial.h`. You are free to add your own helper functions and classes to complete the assignment. Please don’t change the signature of the constructor and execute function. The execute function is expected to accept one of the following commands:

1. ``"load <filename>"``: This command loads the specified file. The file may be specified by either an absolute or a relative pathname. Running this command should result in your program parsing and storing the words in this file in a data structure that can be queried using the locate command (described below). A word is defined as a sequence of upper and lower case letters in the English alphabet (i.e. characters ’a’ to ’z’, and ’A’ to ’Z’), numbers, and the apostrophe. All other characters are considered as white space and will therefore be treated as terminating a word. Two successive load commands should be treated as if there is an intermediate ``"new"`` command (see below) in between the two commands.

2. ``"locate <word> <n>"``: This command outputs the number of the word, counting from the beginning of the file, of the n :superscript:`th` occurrence of the word. Word numbering starts from 1, so the first word in the load file has a word number of 1. `The locate command is case insensitive`, i.e. to match the word in the locate command with a word in the load file you should use a case-insensitive string comparison method. If there are no matches for the locate command, print ``No matching entry``.

   The syntax of the locate command is ``"locate <word> <n>"``. The ``<word>`` parameter will have a whitespace before and after it, and ``<n>`` should be an integer greater than 0.

   As an example, the following are legal commands: ``"locate sing 3"`` and ``"locate  sing  3"`` Both locate the 3rd occurrence of ``sing``, but the second command has a few additional blank spaces around the parameter ``sing``.

   The following commands are not legal: ``"locate sing3"``, ``"locate sing 3q"``. The first command does not specify a parameter `<n>`, and in the second command the parameter `<n>` is not an integer. Please note that the command ``"locate sing23 4"`` is a legal command for locating the fourth occurrence of the word ``sing23``.

3. ``"new"``: This command resets the word list to the original (empty) state.

4. ``"end"``: This command terminates the program.

Here are some of the corner cases that you need to handle:

- If a bad command is entered, print the precise string ``ERROR: Invalid command``, and go to the next prompt. Examples of bad commands are: ``"find word 7"`` and ``"locate song"``. Other examples of bad command include the locate command having a word that is not legal as per the definition above. For example ``ra#s`` and ``rats!`` are invalid word parameters.
- Note that if an incorrect load command is entered, such as ``"load"`` (no filename) then your data structure should not be reset. In other words, if you have a previously loaded file, subsequent locate commands should still query that previously loaded file. Similarly if the load command specifies an invalid file name, then you should not reset the data structure. In both cases of the invalid load command outlined above, please print the standard error message ``ERROR: Invalid command``.
- If there is extraneous content in the command, such as ``"locate word 5 17"`` or ``"new 12"``, print out the standard error message: ``ERROR: Invalid command``
- All the command keywords are case insensitive, so ``"LoCATe sing 2"`` is a valid command, and should be treated as ``"locate sing 2"``.
- Note that all of the characters apart from the alphabet, digits and apostrophe are considered as whitespace. So a term such as ``this.that`` should be treated as two
  separate occurences of the words ``this`` and ``that``.


Example
---------------

Given the following sample text file, sample.txt:

.. topic::  sample.txt

   Sing a song of sixpence,

   A pocket full of rye;
   
   Four and twenty blackbirds
   
   Baked in a pie.
   
   When the pie was opened,
   
   They all began to sing.
   
   Now, wasn't that a dainty dish
   
   To set before the King?

   The King was in his countinghouse,
   
   Counting out his money;
   
   The Queen was in the parlor
   
   Eating bread and honey.
   
   The maid was in the garden,
   
   Hanging out the clothes.
   
   Along there came a big black bird[[
   
   And snipped off her nose!


The following is a sample run:

.. topic::  Sample Run

   > load data/sample.txt 

   > locate song 1
   
   3
   
   > locate Song 1
   
   3
   
   > locate SoNg 1
   
   3
   
   > locate pie 1
   
   18
   
   > locate pie 2
   
   21
   
   > locate pie 3
   
   No matching entry 
   
   > locate prince
   
   ERROR: Invalid command 
   
   > locate prince 1
   
   No matching entry
   
   > new
   
   > locate song 1
   
   No matching entry
   
   > end


Design Task
---------------
Your main design task is to pick a **tree-based** data structure (6422 students `do not use a hash-based index structure!`. 4420 students can.) that allows `efficient` execution of the locate command. You may have several design choices, and I want you to pick the most efficient data structure that you can think of.

For this assignment, I am not concerned with the efficiency of the load command. However, you do have a restriction on the amount of space that you can use for running your program. The memory footprint of your program, which includes the memory used by your code and the data structure that you build, should not exceed four times the size of the input load file, when measured in bytes. Don't worry about exceeding this limit on very small files. For example, it is okay if your program exceeds this limit when loading the small sample load file sample.txt, but on the large file wrnpc.txt it should meet this requirement. You can use the command ``ps -l`` to check the program size. If you are not familiar with ``ps``, please read the `man` page.

Code structure
-----------------
The skeleton code provided to you has the following directory structure:

1. :file:`src/tutorial/tutorial.cc` and :file:`src/include/tutorial/tutorial.h`: Your code goes here. Please don’t change signature of the constructor and existing functions.

2. :file:`test/unit/tutorial/tutorial_test.cc`: Unit test for testing the implementation. (You can add more testcase)

3. :file:`test/unit/data/*`: Sample text files to test your implementation. (You can add new .txt files to test your implementation)

4. :file:`submit.sh`: script to generate zip file which you need to upload to Gradescope

5. :file:`REPORT.md`: Add design related information. See :ref:`submit <submit>` instruction for more information.

6. :file:`CMakeLists.txt`: CMake file for building the project (You need not modify this file)

7. :file:`script/*, third_party/*, .clang-format, .clang-tidy` : supporting build scripts (You need not modify this file)


.. include:: ../shared/logistics.rst

.. include::../shared/collaboration_grading.rst