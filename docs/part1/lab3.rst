Assignment 3: B+-Tree
=================================

For the third lab, you will implement the B+Tree data structure.

Getting started
---------------

**Start by downloading the code for lab 3 from Canvas.**

Description
~~~~~~~~~~~

In this lab, you will implement a B+-Tree index for your database using
the header ``src/include/index/btree.h``.
The B+-Tree must support the following operations: 

* ``lookup`` Returns a value or indicates that the key was not found. 
* ``insert`` Inserts a new key-value pair into the tree. 
* ``erase`` Deletes a specified key. You may simplify the logic by accepting under full pages.

You will use page ids and the buffer manager instead of pointers to
resolve child nodes in the tree. You can assume that the
``buffer_manager`` methods ``fix_page`` and ``unfix_page`` work as
described in the lecture. We added a fake buffer manager that allows you
to test your code without I/O, so you can focus only on implementing the
B+-Tree.    

Implementation Details
~~~~~~~~~~~~~~~~~~~~~~

The index should be implemented as C++ template that accepts the
parameters **key type**, **value type**, **comparator** and **page
size**. Note that this task does not use a dynamic **page size** but
compile-time constants. You therefore have to implement the B+-Tree
primarily in the header ``src/include/index/btree.h`` but you are able
to compute a more convenient node layout at compile-time.

You can test your implementation using the tests in
``test/unit/index/btree_test.cc``. The tests will instantiate your
B+-Tree as ``BTree<uint64_t, uint64_t, std::less<uint64_t>, 1024>``.

Build instructions:
~~~~~~~~~~~~~~~~~~~

Enter BuzzDB's directory and run

.. code-block:: sh

    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make

Test Instructions:
~~~~~~~~~~~~~~~~~~

To run the entire test suite, use:

.. code-block:: sh

    ctest

ctest has a flag option to emit verbose output. Please refer to `ctest
manual <https://cmake.org/cmake/help/latest/manual/ctest.1.html#ctest-1>`__.

We have provided all the test cases for this lab. Gradescope will only
test your code against these test-cases.

Similar to labs 1 and 2, your implementation will be checked for memory
leaks. You can check for memory leaks using valgrind.

.. code-block:: sh

    ctest -V -R btree_test_valgrind

Detailed Instructions
---------------------

Code structure
~~~~~~~~~~~~~~

You will implement the B+-Tree in the ``src/include/index/btree.h``
file. You are provided with three functions in the skeleton code for the
three operations introduced above.

* ``std::optional<ValueT> lookup(const KeyT &key)`` - looks up and returns the value of the node with key specified by the parameter ``key``.
* ``void insert(const KeyT &key, const ValueT &value)`` - Inserts a new key-value pair specified by the parameters ``key`` and ``value`` into the tree. 
* ``void erase(const KeyT &key)`` - Deletes the key specified by the parameter ``key``.


In addition to these functions, we have also provided the skeleton code
for structures that represent different nodes in the B+-Tree:
 
* ``Node`` - This is the base structure for all the nodes in the B+-Tree.
* ``InnerNode`` - This structure should be used to represent the *inner* nodes of the tree. 
* ``LeafNode`` - This structure should be used to represent the *leaf* nodes of the tree.


Note that ``InnerNode`` and ``LeafNode`` inherit members from the base
``Node`` object.

Following are some of the important members from ``InnerNode`` and ``LeafNode`` that you will need to implement:

* ``static constexpr uint32_t kCapacity`` - The capacity of a node. 
* ``std::pair<uint32_t, bool> lower_bound(const KeyT &key)`` - Get the index of the first key that is not less than than a provided key. 
* ``void insert(const KeyT &key, uint64_t split_page)`` - Insert a key.
* ``KeyT split(std::byte* buffer)`` - Split the node.
* ``void erase(const KeyT &key)`` - Erase a key (Used only with leaf nodes).
* ``std::vector<KeyT> get_key_vector()`` - Returns the keys (Used only while testing your implementation). -
* ``std::vector<uint64_t> get_child_vector()`` - Returns the child page ids (Used only while testing your implementation).

Algorithmic details
~~~~~~~~~~~~~~~~~~~

This is a rough outline of the steps you need to follow to implement the
above methods.

-  Lookup

   1. Locate the leaf node corresponding to the key.

      1. Start with the root node.
      2. Is the current node a leaf?

      -  if yes, return the current page.
      -  if no, find next node to traverse (hint: use
         ``inner_node->lower_bound`` method).
      -  repeat b until leaf node is found.

   2. Once leaf node is found, find first entry ≥ search key (use
      *binary\_search*).
   3. If such entry is found, return it, else return no key is found.

-  Insert

   1. Create a new root if needed(code snippet provided).
   2. Locate the appropriate leaf page (same as step (i) in ``lookup``).
   3. Starting with the leaf node, execute the following actions.
   4. Is there free space on the node(leaf/inner)?

      -  If yes, insert entry and stop (use ``node->insert``).

   5. Split the node(leaf/inner) into two (use ``node->split`` method).
   6. Insert key into the appropriate node(leaf/inner).
   7. Insert return value of ``node->split`` as separator into parent.

      -  If the parent overflows, follow steps 4 through 7 on the parent
         node until node with free space is found.

   Note: If any node in the process does not have a parent, create new
   root and update its children.

-  Erase

   1. Lookup the appropriate leaf page (same as step 1 in ``lookup``).
   2. Remove the entry from the current page (use ``leaf_node->erase``
      method). (note that ``erase`` becomes lot easier in our case
      because we allow under full pages)

-  Capacity - Here, you will implement the logic to compute the capacity
   of a given node.

Additional References
---------

`Check out this cool visualization of B+ trees <https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html>`

General Clarifications
---------

1. Each node in the B+tree is a page. The B+tree index can be a large collection of pages on disk (that do not fit in the buffer pool). The B+tree index implementation uses the buffer manager to fetch the relevant pages while searching for a key or inserting/deleting a key.
   - Values in inner nodes are page_ids (uint64_t) of other inner nodes or leaf nodes.
   - Values in leaf nodes are of type ValueT.
   - The separator key in an inner node is equal to the leftmost key in its right child node. So that all keys less than this separator key are in the left child node and all keys >= this  separator key are in the right child node.

2. Set kCapacity
.. code-block:: c++
   static constexpr uint32_t kCapacity =
   (PageSize - sizeof(Node)) / (sizeof(KeyT) + sizeof(ValueT));

3. You will need to add some more member variables in Node. Examples (this list is not complete -- you can get creative here):
.. code-block:: c++
   /// node id
   uint64_t node_id = INVALID_NODE_ID;
   /// parent's node id
   uint64_t parent_node_id = INVALID_NODE_ID;
   /// The level in the tree.
   uint16_t level;

4. Instantiate an object from the char buffer
.. code-block:: c++
   KeyT split(char *buffer) {
   auto ``*right_inner_node`` = new (buffer) InnerNode();
   ...

5. Ignore the last element in keys for InnerNode (since it has kCapacity-1 keys and kCapacity values).

6. Nodes are connected using node_id. Example:
.. code-block:: c++
      node_id = inner->children[result.first];
      buffer_manager.unfix_page(*frame, false);
      frame = &buffer_manager.fix_page(node_id, false);
      node = reinterpret_cast<Node *>(frame->get_data());

7. To simplify your implementation, you can allow underflow in nodes (i.e., you need not merge nodes with no keys).

8. Initialize an inner node in this manner:
.. code-block:: c++
   inner->children[0] = left_node_id;
   inner->count++;
   Then add an entry in this manner
.. code-block:: c++
   inner->insert(parent_key, parent_node_id)
   inner node can store kCapacity-1 keys and kCapacity pointers
   Do not use the last slot in keys for inner node.

Logistics
---------

You must submit your code (see below) as well as an one-page writeup (in a file named `REPORT.md`) describing your solution. In the writeup, mention: (1) the design decisions you made, and (2) the missing components in your code. We will award partial credits based on this writeup (if you are unable to finish the implementation before the due date and/or if it fails any test cases).

Collaboration
~~~~~~~~~~~~~

This is an individual assignment. No collaboration is allowed.

Submitting your assignment 
~~~~~~~~~~~~~~~~~~~~~~~~~~~

You should submit your code as a zip file via Gradescope. We have set up an autograder that will test your implementation. You are allowed to make multiple submissions and we will use the latest submission to grade your lab.

.. code-block:: sh

  bash submit.sh <name>

> :warning: **WARNING** Do not add additional files to the zip file, use the ``submit.sh`` script.  


Grading
~~~~~~~

100% of your grade will be based on whether or not your code passes the autograder test suite. We will award partial marks for submissions that fail the autograder test suite (based on the writeup).

FAQs
---------
1. Can you clarify the terminologies used in this assignment?
   - Key - Key of an element you want to insert/lookup/erase from the dictionary

   - Value - corresponding value associated with the key

   - Page ID - same as what we had in Assignment 2 (linked to a page in the segment)

   - Node ID - No such concept as node ID. But this can be treated to be same as Page ID because in our implementation, one node is stored per page

2. What are differences between the split methods for inner node and leaf node?
   - Both the inner and leaf node's split method should create one new node (it should use the memory address input ``*buffer`` to do so). So the node on which the split method is called will shrink and lend some keys and children/value to the newly created node
   - The assigning of parent pointer can be done inside (since both the old and newly created node would have the same parent), while setting the child pointers in parent can be done outside the method (since the split key is anyway returned from the split method) 

3. Will the node's keys array have duplicate values?
   Repeated inserts with the same key should be treated as "updates".
   Ex. if a (K1, V1) already exists in the tree and we call insert(K1, V2), then lookup(K1) should return V2.

4. How to fix/unfix a page?
   - BufferManager provides a way to access individual nodes (via page ID).
   - In our implementation, we store one node per page (so you could presumably consider them to be one-to-one mapped). So, whenever you want to access any node of the tree, you would need to ask the buffer manager to fix the page (using PageID) which stores that node. The buffer manager would return you the buffer frame after fixing that page in the pool and you can then use the get_data() method on the frame to get the node.
   - After you are done using that page, you should unfix the page by calling the appropriate method on the buffer manager.
   - Inner Nodes have keys as regular keys and values as page IDs of its children inner/leaf nodes. Leaf Nodes have keys as regular keys and values as the actual values stored in the B+Tree.

5. Is it mandatory to use Binary Search?
   Yes, we recommend using a binary search. But we don't deduct a lot of points in the case you use linear search.


