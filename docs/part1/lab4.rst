Assignment 4: Operators
=======================   

In the fourth lab, you will be implementing physical operators using the iterator model. 

.. include:: ../shared/getting_started.rst

Description
----------------

In this lab, you will implement physical operators for your database.

Your code needs to support the following operations:

* **Print**: Prints tuples separated by newlines. Attributes should be separated
  with a comma without any extra spaces.
* **Projection**: Generates tuples with a subset of attributes.
* **Select**: Filters tuples by a given predicate. Each predicate consists of one
  relational operator (`l == r`, `l != r`, `l < r`, `l <= r`, `l > r`, `l >= r`)
  where `l` stands for an attribute and `r` stands for an attribute or a
  constant.
* **Sort**: Sorts tuples by the given attributes and direction (ascending or
  descending).
* **HashJoin**: Computes the inner equi-join of two inputs on one attribute.
* **HashAggregation**: Groups and calculates (potentially multiple) aggregates on
  the input.
* **Union**: Computes the union of two inputs with set semantics.
* **UnionAll**: Computes the union of two inputs with bag semantics.
* **Intersect**: Computes the intersection of two inputs with set semantics.
* **IntersectAll**: Computes the intersection of two inputs with bag semantics.
* **Except**: Computes the difference of two inputs with set semantics.
* **ExceptAll**: Computes the difference of two inputs with bag semantics.

.. include:: ../shared/environment_setup.rst

Implementation Details
----------------

Add your implementation to the files `src/include/operators/operators.h` and `src/operators/operators.cc`. There you can find the `Register` class and one class for each operator. All definitions for the methods that you should implement are provided. You can add new member functions and member variables to all classes as needed.

Your implementation should pass all tests starting with `OperatorsTest`. The tests `AdvancedOperatorsTest` and `BonusOperatorsTest` are not mandatory for this assignment. You can complete the assignment without passing these tests and still receive full marks. These tests are only for your own learning and practice. They do not give you any extra credit.

.. include:: ../shared/logistics.rst

Detailed Instructions
----------------------

You will add your implementation to the `src/operators/operators.cc` and `src/include/operators/operators.h` files. We provide you with the skeleton code for all the operators introduced above (one class for each operator) and a `Register` class. You will need to add the necessary member variables to the class definitions of each operator in the `src/include/operators/operators.h` file. The functionality of each operator is provided in this header file. Please read through the header file carefully before you start your implementation.

First, you will implement the `Register` class. The `Register` class is used to pass tuples between the operators. Each instance of the `Register` class corresponds to a single attribute. It should support storing 64 bit signed integers and fixed size strings of length 16.

Then you can implement the operator classes. On a high level, each operator is characterized by three functions:
  
  * `open()` : Initialize an operator.
  * `next()` : Try to generate the next tuple; Return true when a new tuple is available.
  * `close()` : Destroy the operator.

You will implement these functions for all the operators listed above. Where applicable, you will also implement the `get_output()` function. This returns the pointers to the registers of the generated tuple. When `next()` returns true, the Registers will contain the values for the next tuple. Each `Register*` in the vector stands for one attribute of the tuple.

To help you understand the semantics of the code, we are providing you with the implementation of the `Print` operator here. Note that the actual implementation for each operator may vary significantly.

.. code-block:: c++

  //operator.h

  class Print : public UnaryOperator {
   private: // Add your member variables here
    /// Stream of data
    std::ostream& stream;

   public:
    Print(Operator& input, std::ostream& stream);

    ~Print() override;

    void open() override;
    bool next() override;
    void close() override;
    std::vector<Register*> get_output() override;
  };

.. code-block:: c++

    // operator.cc

    Print::Print(Operator& input, std::ostream& stream)
        : UnaryOperator(input), stream(stream) {}

    Print::~Print() = default;

    void Print::open() { input->open(); }

    bool Print::next() {
      if (input->next()) {
        std::vector<Register*> input_tuple = input->get_output();
        size_t reg_itr = 0;
        size_t tuple_size = input_tuple.size();
        for (auto reg : input_tuple) {
          if (reg->get_type() == Register::Type::INT64) {
            stream << reg->as_int();
          } else if (reg->get_type() == Register::Type::CHAR16) {
            stream << reg->as_string();
          }

          if (reg_itr++ != tuple_size - 1) {
            stream << ',';
          }
        }
        stream.put('\n');
        return true;
      }
      return false;
    }

    void Print::close() {
      input->close();
      stream.clear();
    }

    std::vector<Register*> Print::get_output() {
      // Print has no output
      return {};
    }

.. include::../shared/collaboration_grading.rst
