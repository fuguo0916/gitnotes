# Generic Algorithms
2022.05.22

## Overview
Iterators make the algorithms container independent, but algorithms do depend on element-type operations. The fact that the algorithms operate in terms of iterators and not container operations has a perhaps surprising but essential implication: Algorithms never change the size of the underlying container. Algorithms may change the values of the elements stored in the container, and they may move elements around within the container. They do not, however, ever add or remove elements directly.

As we’ll see, there is a special class of iterator, the inserters, that do more than traverse the sequence to which they are bound. When we assign to these iterators, they execute insert operations on the underlying container. When an algorithm operates on one of these iterators, the iterator may have the effect of adding elements to the container. The algorithm itself, however, never does so.

## A First Look At Algorithms
The `accumulate` function, defined in `numeric`, takes three arguments. The first two specify a range of elements to sum. The third is an initial value for the sum. The type of the third argument to `accumulate` determines which addition operator is used and is the type that `accumulate` returns.

Algorithms that take a single iterator denoting a second sequence assume that the second sequence is at least as large at the first.

### Introducing `back_inserter`
An **insert iterator** is an iterator that adds elements to a container. When we assign through an insert iterator, a new element equal to the right-hand value is added to the container. `back_inserter`, defined in `iterator` header, takes a reference to a container and returns an insert iterator bound to that container. When we assign through that iterator, the assignment calls `push_back` to add an element with the given value to the container:
```c++
vector<int> vec;
back_insert_iterator it = back_inserter(vec);
*it = 42;
fill_n(it, 100, 10);
```

## Customizing Operations
### Pridicates
A predicate is an expression that can be called and that returns a value that can be used as a condition. The predicates used by library algorithms are either unary predicates or binary predicates.
```c++
bool is_shorter(const string &s1, const string &s2)
{
    return s1.size() < s2.size();
}
sort(words.begin(), words.end(), is_shorter);
```

### Lambda Expressions
We can pass any kind of **callable object** to an algorithm. The only callables we've used so far are functions and function pointers. There are two other kinds of callables: classes that overload the function-call operator, which we’ll cover in Chapter 14, and lambda expressions.
```plain
[capture list] (parameter list) -> return type { function body }
```
Capture list is an (often empty) list of local variables defined in the enclosing function. The capture list is used for local nonstatic ariables only; lambdas can use local static varibles and those declared outside the function directly. We can omit either or both of the parameter list and return type but must always include the capture list and function body. If we omit the return type, the lambda has an inferred return type that depends on the code in the function body. If the function body is just a `return` statement, the return type is inferred from the type of the expression that is returned. Otherwise, the return type is `void`. Unlike ordinary functions, a lambda may not have default arguments.

When we define a lambda, the compiler generates a new (unnamed) class type that corresponds to that lambda. We’ll see how these classes are generated in Chapter 14. For now, what’s useful to understand is that when we pass a lambda to a function, we are defining both a new type and an object of that type. By default, the class generated from a lambda contains a data member corresponding to the variables captured by the lambda. Like the data members of any class, the data members of a lambda are initialized when a lambda object is created.

```c++
[]          // empty capture list
[names]     // a comma-seperated list of names local to the enclosing function.
            // each name may be preceded by &
[&]         // Implicitly by reference capture list
[=]         // Implicitly by value capture list
[&, identifier_list]
            // implicitly by reference capture list
            // varibles in identifier_list are captured by value
[=, reference_list]
            // implicitly by value capture list
            // varibles in reference_list may not include `this` must be preceded by &
            // varibles in reference_list are captured by reference
```

Because the value is copied when the lambda is created, subsequent changes to a captured variable have no effect on the corresponding value inside the lambda. When we capture a variable by reference, we must ensure that the variable exists at the time that the lambda executes.

By default, a lambda may not change the value of a variable that it copies by value. If we want to be able to change the value of a captured variable, we must follow the parameter list with the keyword mutable. Lambdas that are mutable may not omit the parameter list.
```c++
auto f = [v1] () mutable { return ++v1; };
```

The explicit declaration of return type is needed if there are mutiple return statements.

### Binding Arguments
The `bind` function, which is defined in `functional` header, can be thought of as a general-purpose function adaptor. It takes a callable object and generates a new callable that “adapts” the parameter list of the original object. The general form of a call to bind is:
```c++
auto new_callable = bind(callable, arg_list);
```
where `new_callable` is itself a callable object and `arg_list` is a comma-separated list of arguments that correspond to the parameters of the given callable . That is, when we call `new_callable`, `new_callable` calls `callable` , passing the arguments in `arg_list`.

The arguments in `arg_list` may include names of the form `_n` , where `n` is an integer. These arguments are “placeholders” representing the parameters of `new_callable`. They stand “in place of” the arguments that will be passed to `new_callable`.
```c++
auto check6 = bind(check_size, _1, 6);
```
The `_n` names are defined in a namespace named `placeholders`. That namespace is itself defined inside the `std` namespace. By default, the arguments to `bind` that are not placeholders are copied into the callable object that `bind` returns. However, as with lambdas, sometimes we have arguments that we want to bind but that we want to pass by reference or we might want to bind an argument that has a type that we cannot copy.
```c++
auto f = bind(print, ref(os), _1, ' ');
```
The `ref` function returns an object that contains the given reference and that is itself copyable. There is also a `cref` function that generates a class that holds a reference to const. Like `bind`, the `ref` and `cref` functions are defined in the `functional` header.

## Revisiting Iterators
The library defines several additional kinds of iterators in the `iterator` header, which include:
- **Insert iterator**: These iterators are bound to a container and can be used to insert elements into the container.
- **Stream iterators**: These iterators are bound to input or output streams and can be used to iterate through the associated IO stream.
- **Reverse iterators**: These iterators move backward, rather than forward. The library containers, other than forward_list, have reverse iterators.
- **Move iterators**: These special-purpose iterators move rather than copy their elements. We’ll cover move iterators in Chapter 13.

### Insert Iterators
When we assign a value through an insert iterator, the iterator calls a container operation to add an element at a specified position in the given container.
```c++
it = t              // insert the value denoted by t
*it, ++it, it++     // Do nothing to it and return it
```
- `back_inserter` creates an iterator that uses `push_back`.
- `front_inserter` creates an iterator that uses `push_front`.
- `inserter` creates an iterator that uses `insert`. This function takes a second argument, which must be an iterator into the given container.  Elements are inserted ahead of the element denoted by the given iterator.

### `iostream` Iterators
```c++
/* istream_iterator operations */
istream_iterator<T> in(is);
istream_iterator<T> end;
in1 == in2      // in1 and in2 must read the same type. They are equal if they are both 
                // the end value or are bound to the same input stream
in1 != in2
*in             // returns the value read from the stream
in->mem         // synonym for (*in).mem
++in, in++      // reads the next value from the input stream using the >> operator for
                // the element type. As usual, the prefix version returns a reference to
                // the incremented iterator. The post version returns the old value.

/* ostream_iterator operations */
ostream_iterator<T> out(os);
ostream_iterator<T> out(os, d);     // d is a character array
out = val           // Writes val to the ostream to which out is bound using the << operator.
*out, ++out, out++  // do nothing to out and return out
```
```c++
istream_iterator<int> in_iter(cin); // read ints from cin
istream_iterator<int> eof; // istream ''end'' iterator
while (in_iter != eof)
    vec.push_back(*in_iter++);
```
```c++
istream_iterator<int> in_iter(cin), eof; // read ints from cin
vector<int> vec(in_iter, eof); // construct vec from an iterator range
```

When we bind an `istream_iterator` to a stream, we are not guaranteed that it will read the stream immediately. The implementation is permitted to delay reading the stream until we use the iterator. We are guaranteed that before we dereference the iterator for the first time, the stream will have been read. We may call the `reverse_iterator`'s `base` member to get its corresponding ordinary iterator. `rcomma` and `rcomma.base()` refer to different elements, as do `line.crbegin()` and `line.cend()`. These differences are needed to ensure that the range of elements, whether processed forward or backward, is the same.

## Structure of Generic Algorithm
### The Five Iterator Categories
```c++
Input Iterator              // Read, but not write; single-pass, increment only
Output Iterator             // Write, but not read; single-pass, increment only
Forward Itertor             // Read and write; multi-pass, increment only
Bidirectional Iterator      // Read and write; multi-pass, increment and decrement
Random-access Iterator      // Read and write; multi-padd, full iterator arithmetic
```
### Algorithm Naming Conventions
- Some Algorithms Use Overloading to Pass a Predicate
- Algorithms with _if Versions
- Distinguishing Versions That Copy from Those That Do Not

## Container-Specific Algorithms
```c++
/* algorithms that are members of list and forward_list */
lst.merge(lst2)
lst.merge(lst2, comp)
lst.remove(val)
lst.remove_if(pred)
lst.sort()
lst.sort(comp)
lst.unique()
lst.unique(pred)

/* agrs of lst.splice(args) or flst.splice_after(args) */
(p, lst2)
(p, lst2, p2)
(p, lst2, b, e)
```

## References
- [algorithm on cpp reference](https://www.cplusplus.com/reference/algorithm/)