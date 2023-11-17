Assignment 2: Buffer Manager
==================================

For the second lab, you will implement the buffer manager that uses the
2Q replacement strategy. We have provided you with a set of
unimplemented functions. You will need to fill in these functions.

We **strongly recommend** that you start as early as possible on this
lab. It requires you to write a fair amount of code.

.. include:: ../shared/getting_started.rst

Description
------------

In this lab, you need to implement a buffer manager that uses the 2Q
replacement strategy. The implementation should take 64 bit page ids and
return pages that can be locked (shared or exclusive), read and written.
The 64 bits of a page id are divided into a segment id and a segment
page id. The most significant 16 bits are the segment id and the least
significant 48 bits are the segment page id. When a page is written to
disk, it must be stored in a file whose filename is the segment id. Your
implementation should support different page and buffer sizes and be
able to fix and unfix pages concurrently from different threads.

.. include:: ../shared/environment_setup.rst

Implementation Details
----------------------

We provide you with the skeleton code required to implement the buffer
manager. Add your implementation to the files
``src/include/buffer/buffer_manager.h`` and
``src/buffer/buffer_manager.cc``. There you can find the classes
``BufferFrame`` which represents a page and ``BufferManager`` which
represents the buffer manager. All definitions for the methods that you
should implement are provided. You can add new member functions and
member variables to either class as needed.

It may be useful to use the class ``buzzdb::File`` from
``src/include/storage/file.h``. Note that only ``File.read_block()`` and
``File.write_block()`` are thread-safe. Also, using multiple ``File``
objects that refer to the same file concurrently also leads to
unexpected results. Also, to ensure thread-safety you can use
`std::mutex <https://en.cppreference.com/w/cpp/thread/mutex>`__
and
`std::shared_mutex <https://en.cppreference.com/w/cpp/thread/shared_mutex>`__.

Your implementation should pass all of tests starting with
``BufferManagerTest``. Tests whose name contains ``Multithread`` are
executed with a timeout of 30s. This is done so that a deadlock does not
block the tester.

We will also grade your implementation based on its efficiency. You
don’t have to optimize on assembly level or try to improve the runtime
by microseconds. Instead, we want you to especially take care when
holding latches. They should be held as short as possible to enable
better concurrency. Most importantly, latches should not be held while
reading from or writing to disk as this essentially blocks all other
threads for the duration of the I/O operation which can be many
milliseconds.

While solving this assignment, we encourage you to first complete 
the single-threaded implementation before moving to multi-thread test cases.

.. include:: ../shared/logistics.rst

**NOTE**: Students in CS4420 are not required to pass the multithreading
test cases (The ones with their name starting with "Multithread").
However, for students in CS6422, passing all test cases is mandatory to
obtain full credit for the assignment.


Algorithm details
----------------------

This is a rough outline of the steps you can follow to implement the
above methods.

-  fix_page

   1. First check if page is in LRU queue. If found, lock the frame and return.
   2. Search for page in FIFO queue. If it is found, save page in LRU queue and delete from FIFO. Lock the frame and return.
   3. If the page is not found in either queue, find a free slot.
         1. If the current page counter is less than the capacity, simply increment the counter to find the free frame id.
         2. If the page counter exceeds the capacity, check if we can find a free slot (i.e use counter for the frame is 0) in FIFO queue first or the LRU queue.
         3. If we are unable to find a free slot, throw buffer full error.
   4. Once we find a free frame id, lock the frame, read the corresponding page data, push it into the FIFO queue and return.
   5. NOTE :
         1. Make sure that the locking / unlocking is done based on the type of access that is needed (i.e shared or exclusive)
         2. Each lock needs to increment the frame use counter. Similarly each unlock needs to decrement the use counter.
         3. If we find a free slot that is dirty after exceeding the capacity, we need to write the data in the frame before resetting it. This write needs to be done under an exclusive lock.


-  unfix_page

   1. Mark the page as dirty if not already done so.
   2. Decrement the use counter for the frame.
   3. Unlock the frame. 


General Clarifications
----------------------

- A basic structure of a BufferFrame is given below. This information should be sufficient to build a working implementation, but you are free to add other features based on your logic::

         BufferFrame::BufferFrame()
         : page_id(INVALID_PAGE_ID),
         frame_id(INVALID_FRAME_ID),
         dirty(false),
         data(other.data),
         exclusive(false) {}

- Please do not modify any file other than `buffer_manager.cc` and `buffer_manager.h`. The other files we have provided have helper methods that you can call from your implementation and are not meant to be modified. You are, however, free to add new files that you would like to implement and use.

- One file that we have provided, which should be of interest to you is `file.h`, which can be used for file operations. For example, a read and write can be performed as follows::

         auto file_handle = File::open_file(std::to_string(segment_id).c_str(), File::WRITE);
         file_handle->read_block(start, page_size_, pool_[frame_id]->data.data());

- There are a lot of good references for understanding the 2Q algorithm. The course slide deck gives a good gist of it, which should be the basis of your implementation. `This <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__ is my favourite external reference to *understand* it.

- Please note that you must get locks when accessing the queues: This is a simple mistake, but it is the crux of your implementation - without this, your buffer management implementation would not work.

.. include::../shared/collaboration_grading.rst
