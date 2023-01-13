.. role:: bash(code)
   :language: bash

.. include:: ../.colors.rst


Assignment 2: Logging And Recovery
==================================

**Due: 24/2/2022 11:59 PM EST**

Project Description
-------------------
The goal of the lab is to implement ``Logging`` and ``Recovery`` mechanism in buzzdb. You need to implement a write ahead logging (WAL) under No-Force/Steal policy. You need to log every page-level write operation and transaction command.

Program Specification
-----------------------
We have provided you with ``LogManager`` stub that contains APIs that you need to implement.
You should not modify the signature of the pre-defined functions. You should also not modify any predefined member variables.
You are however allowed to add new helper private functions/member variables in order to correctly realize the functionality.


.. note:: 
   You **only** need to modify the code in ``src/log/log_manager.cc`` and ``src/include/log/log_manager.h``. 

The lab can be divided into two tasks: 

Task #1 - Logging
~~~~~~~~~~~~~~~~~~

To achieve the goal of atomicity and durability, the database system must output to stable storage information describing the modifications made by any transaction, this information can help us ensure that all modifications performed by committed transactions are reflected in the database (perhaps during the course of recovery actions after a crash). It can also help us ensure that no modifications made by an aborted or crashed transaction persist in the database. The most widely used structure for recording database modifications is the log. The log is a sequence of log records, recording all the update activities in the database. 
The buzzdb database system has a global ``LogManager`` object which is responsible logging information. 
You need not worry about making the calls to the LogManager. The other components of the system are responsible for 
correctly calling the ``Log Manager``. The `HeapSegment` will explicitly call the `log_update` before making any update operations. The ``TransactionManager`` will invoke ``log_txn_begin`` when a transaction begins, ``log_commit`` before commiting a transaction and ``log_abort`` in case a transaction has to be aborted.


API Requirements and Hints
<<<<<<<<<<<<<<<<<<<<<<<<<<<<

.. highlight:: c++

.. code-block:: c++
   
   // Returns the count of total number of log records in the log file 
   void LogManager::get_total_log_records()

   /** 
   * Increment the ABORT_RECORD count.
   * Rollback the provided transaction.
   * Add abort log record to the log file.
   * Remove from the active transactions.
   */
   void LogManager::log_abort(uint64_t txn_id, BufferManager& buffer_manager)

   /**
   * Increment the COMMIT_RECORD count
   * Add commit log record to the log file
   * Remove from the active transactions
   */ 
   void LogManager::log_commit(uint64_t txn_id)
   
   /**
   * Increment the UPDATE_RECORD count
   * Add the update log record to the log file
   */
   void LogManager::log_update(uint64_t txn_id, uint64_t page_id, uint64_t length, uint64_t offset, std::byte* before_img, std::byte* after_img)
   

   /**
   * Increment the BEGIN_RECORD count
   * Add the begin log record to the log file
   * Add to the active transactions
   */ 
   void LogManager::log_txn_begin( uint64_t txn_id)

   /**
   * Increment the CHECKPOINT_RECORD count
   * Flush all dirty pages to the disk (USE: buffer_manager.flush_all_pages())
   * Add the checkpoint log record to the log file
   */ 
   void LogManager::log_checkpoint( BufferManager& buffer_manager)


Task #2 - Recovery
~~~~~~~~~~~~~~~~~~

The next part of the lab is to implement the ability for the DBMS to recover its state from the log file.


API Requirements and Hints
<<<<<<<<<<<<<<<<<<<<<<<<<<<<

.. highlight:: c++

.. code-block:: c++


   /**
   * Recover the state of the DBMS after crash. It is broken down into three phases:
   *
   * @Analysis Phase: 
   * 		1. Get the active transactions and commited transactions 
   * 		2. Restore the txn_id_to_first_log_record
   * @Redo Phase:
   * 		1. Redo the entire log tape to restore the buffer page
   * 		2. For UPDATE logs: write the after_img to the buffer page
   * 		3. For ABORT logs: rollback the transactions
   * 	@Undo Phase
   * 		1. Rollback the transactions which are active and not commited
   */ 
   void LogManager::recovery(BufferManager& buffer_manager)


   /**
    * Use txn_id_to_first_log_record to get the begin of the current transaction
   * Walk through the log tape and rollback the changes by writing the before
   * image of the tuple on the buffer page.
   * Note: There might be other transactions' log records interleaved, so be careful to
   * only undo the changes corresponding to current transactions.  
   */ 
   void LogManager::rollback_txn(uint64_t txn_id,  BufferManager& buffer_manager);


For this lab, you are free to choose any data structure for the log record. Here, we provide an example log record structure that you might find helpful.

.. code-block:: json

   {
      "BEGIN_RECORD or COMMIT_RECORD or ABORT_RECORD":[
         "LOGTYPE",
         "TXN_ID"
      ],
      "UPDATE_RECORD":[
         "LOGTYPE",
         "TXN_ID",
         "PAGEID",
         "LENGTH",
         "OFFSET",
         "BEFORE_IMG",
         "AFTER_IMG"
      ],
      "CHECKPOINT_RECORD":[
         "LOGTYPE",
         "ACTIVE_TXNS",
         "TXN_1",
         "TXN_1_FIRST_LOG_OFFSET",
         "...",
         "TXN_K",
         "TXN_K_FIRST_LOG_OFFSET"
      ]
   }

In order to simplify the complexity of the lab, we provide you with an implementation of ``BufferManager``. We have also provided you with
a ``File`` class which has read/write interfaces that will be helpful. 

.. code-block:: c++

   /**
   * Functionality of the buffer manager that might be handy

   Flush all the dirty pages to the disk 
      buffer_manager.flush_all_pages(): 

   Write @data of @length at an @offset the buffer page @page_id 
      BufferFrame& frame = buffer_manager.fix_page(page_id, true);
      memcpy(&frame.get_data()[offset], data, length);
      buffer_manager.unfix_page(frame, true);

   * Read and Write from/to the log_file
      log_file_->read_block(offset, size, data);
      
      Usage:
      uint64_t txn_id;
      log_file_->read_block(offset, sizeof(uint64_t), reinterpret_cast<char *>(&txn_id));
      log_file_->write_block(reinterpret_cast<char *> (&txn_id), offset, sizeof(uint64_t));
   */



To successfully pass all the test cases, you need to add required implementation in the ``log_manager.cc`` and ``log_manager.h``.
We have also added detailed comments in the code skeleton.

.. note::
   **What are the files like slotted_page.cc, heap_file.cc, etc?**
   
   These files are related to the storage manager. Heap file organization is one way of managing pages in the files on disk in a database. A heap file can be treated as an unordered collection of pages with tuples stored in random order. And slotted pages refer to the layout schema of these pages (how the tuples are stored in a page). You don't need to modify or change anything in these files for this assignment. Assume them as black-boxes that take care of the tuple inserts.


Prerequisites
~~~~~~~~~~~~~~

You need to follow the instructions mentioned in the `setup <setup.html>`__ document. 

Download the handout shared via Canvas. You need to copy the handout to the VM (skip this if you are not using VM).

.. code-block:: bash

   # Copy the handout to the VM
   [host] $ vagrant scp [path_to_handout] default:buzzdb-logging-recovery.zip
   [host] $ cd buzzdb
   # Get into VM 
   [host] $ vagrant ssh
   # unzip the handout 
   [vm] $ unzip buzzdb-logging-recovery.zip


 
Instructions for execution
~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. highlight:: bash
   

.. code-block:: bash
   
   [vm] $ cd buzzdb-logging-recovery
   [vm] $ mkdir build 
   [vm] $ cd build
   [vm] $ cmake -DCMAKE_BUILD_TYPE=Release ..
   [vm] $ make
   [vm] $ ctest

We treat compiler warnings as errors. Your project will fail to build if there are any compiler warnings.


General Instructions
----------------------

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


Grading
---------

This assignment is worth 10% of your grade. The maximum score on this assignment is 100.

Frequently Asked Questions
---------------------------
* In the analysis phase, are we meant to get the active transaction information from the latest checkpoint, then read forward from that point in the log to find any other started or committed transactions?

   * Yes, that is correct.
   
* Why do we need to redo the entire log in the redo phase? Wouldn't it be sufficient to redo the log from the latest checkpoint (assuming there is one, otherwise we would certainly have to replay the entire log)?

   * Yes, you redo from the latest checkpoint.

* In the undo phase, should we write an abort record to the log before rolling back a transaction, or is it sufficient to roll back the transaction and remove it from the active transactions?

   * You need to roll back any changes done by the transaction and remove them from the active transactions. No need to log an abort record.\

* What is the offset of the log file, and how to use it?

   * The `offset` helps to maintain where to write in the log file. You can think of it seeking the `offset` number of bytes in the file and then reading or writing.

* I wanted to confirm that apart from maintaining log records, we also need to maintain the ATT and DPT data structures for the recovery phase right?

   * Yes, you have to maintain the ATT data structure. You might not need DPT but it's up to your design.

* I am a bit stuck on the rollback_txn. I walk through the log tape and find all update records in a given transaction, but how do I rollback the changes? It feels like I would have to call heap manager and write the change to the page. Or do I just create a new Update record with the inverse of the update I am reversing?

   * You can use `BufferManager::fix_page()` and then write the change into the page
