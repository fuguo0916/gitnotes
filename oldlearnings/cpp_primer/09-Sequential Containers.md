# Sequential Containers
2022.05.19
## Overview of the Sequential Containers
## Container Library Overview
- Some operations provided by all container types.
```plain
Type Aliases
iterator
const_iterator
size_type
difference_type   
value_type          Element type
reference           synonym for `value_type &`
const_reference     synonym for `const value_type &`

Construction
C c;
C c1(c2); / C c1 = c2;
C c(b, e);
C c{a, b, c, ...};
C c = {a, b, c, ...};

Assignment and Swap
c1 = c2
c1 = {a, b, c, ...}
a.swap(b)
swap(a, b)

Size
c.size()            not valid for `foward_list`
c.max_size()
c.empty()

Add/Remove Elements (not valid for array)
Note: the interface to these operations varies by container type
c.insert(args)
c.emplace(inits)
c.erase(args)
c.clear()

Equality and Relational Operators
==, !=; <, >, <=, >=

Obtain Iterators
c.begin()   c.end()
c.cbegin()  c.cend()

Additional Members of Reversible Containers (not valid for forward_list)
reverse_iterator
const_reverse_iterator
c.rbegin()  c.rend()
c.crbegin() c.crend()
```
- Other operations are specific to the sequential, the associative, or the unordered containers.
```plain
Constructors that take a size are valid for sequetial containers (not including array) only
C seq(n);       not valid for string
C seq(n, t);
```
- Still others are common to only a smaller subset of containers.
### Initializing a Container as a Copy of Another Container
To create a container as a copy of another container, the container and element types must match. When we pass iterators, there is no requirement that the container types be identical. Moreover, the element types in the new and original containers can differ as long as it is possible to convert the elements we’re copying to the element type of the container we are initializing.

### Library `array` Has Fixed Types
Unlike the other containers, a default-constructed array is not empty: It has as many elements as its size. These elements are default initialized just as are elements in a built-in array.

### Container Assignment Function
```c++
// `assign` operations not valid for associative containers or `array` 
seq.assign(b, e)
seq.assign(il)
seq.assign(n, t)
/* Assignment related operations invalidates iterators, references, and pointers into 
   the left-hand container. Aside from `string` they remain valid after a `swap`, and 
   (excepting `array`) the containers to which they refer are swapped. */
```
With the exception of arrays, swapping two containers is guaranteed to be fast—the elements themselves are not swapped; internal data structures are swapped.

## Sequential Container Operations
### Adding Elements to a Sequential Container
```c++
/**************************************************************************************
 * These operations change the size of the container; they are not supported by `array`
 * `forward_list` has special versions of `insert` and `emplace`
 * `push_back` and `emplace_back` not valid for `forward_list`
 * `push_front` and `emplace_front` not valid for `vector` or `string`
 *************************************************************************************/
c.push_back(t)              // void
c.emplace_back(args)        // void
c.push_front(t)             // void
c.emplace_front(args)       // void
c.insert(p, t)              // iterator referring to the added element
c.emplace(p, args)          // iterator referring to the added element
c.insert(p, n, t)           // iterator referring to the first added element, or p if none
c.insert(p, b, e)           // iterator referring to the first added element, or p if none
c.insert(p, il)             // iterator referring to the first added element, or p if none
```
Adding elements to a `vector`, `string`, or `deque` potentially invalidates all existing iterators, references, and pointers into the container. Iterators denoting the range to copy from must not refer to the same container as the one we are changing.

### Accessing Elements
```c++
// `at` and subscript operator valid only for `string`, `vector`, `deque`, and `array`
// `back` not valid for `forward_list`
// Items below all return reference to the element.
// If the container is a const object, the return is a reference to const.
c.back()        // undefined if c is empty
c.front()       // undefined if c is empty
c[n]            // undefined if n >= c.size()
c.at(n)         // throws an `out_of_range` exception if n is out of range
```
### Erasing Elements
```c++
// These operations are not supported by `array`
// `forward_list` has a special version of `erase`
// `pop_back` not valid for `forward_list`
// `pop_front` not valid for `vector` or `string`
c.pop_back()        // void. undefined if c is empty
c.pop_front()       // void. undefined if c is empty
c.erase(p)          // an iterator to the element afer the deleted one.
                    // undefined if p is the off-the-end iterator
c.erase(b, e)       // an iterator to the element after the last one that is deleted
c.clear()           // void
```
Removing elements anywhere but the beginning or end of a `deque` invalidates all iterators, references, and pointers. Iterators, references, and pointer to elements after the erasure point in a `vector` or `string` are invalidated. The members that remove elements do not check their argument(s). Ensure that elements exist before removing them.

### Specialized `forward_list` Operations
`forward_list` does not define `insert`, `emplace`, or `erase`. Instead it defines members named `insert_after`, `emplace_after`, and `erase_after`.
```c++
lst.before_begin()
lst.cbefore_begin()

lst.insert_after(p, t)
lst.insert_after(p, n, t)
lst.insert_after(p, b, e)
lst.insert_after(p, il)

emplace_after(p, args)

lst.erase_after(p)
lst.erase_after(b, e)
```

### Resizing a Container
If the current size is greater than the requested size, elements are deleted from the back of the container; if the current size is less than the new size, elements are added to the back of the container.
```c++
c.resize(n)
c.resize(n, t)
```

### Container Operations May Invalidate Iterators
Iterators, pointers, and references to a deque are invalid if we add elements anywhere but at the front or back. If we add at the front or back, iterators are invalidated, but references and pointers to existing elements are not. All other iterators, references, or pointers to a deque are invalidated if the removed elements are anywhere but the front or back. If we remove elements at the back of the deque, the off-the-end iterator is invalidated but other iterators, references, and pointers are unaffected; they are also unaffected if we remove from the front.

## How a `vector` Grows
```c++
// `shrink_to_fit` valid only for vector, string, and deque
// `capacity` and `reserve` valid only for vetor and string
c.shrink_to_fit()   // free to ignore the request
c.capacity()
c.reserve(n)        // allocate space for at least n elements
```
If the requested size is less than or equal to the existing capacity, `reserve` does nothing. The `resize` members change only the number of elements in the container, not its capacity. We cannot use `resize` to reduce the memory a container holds in reserve. Each `vector` implementation can choose its own allocation strategy. However, it must not allocate new memory until it is forced to do so.


## Additional `string` Operations
```c++
/* other ways to costruct strings */
string s(cp);
string s(s2, pos2);
string s(s2, pos2, len2);

/* substring operation */
s.substr(pos, n)    // pos defaults to 0, n defaults to a value that cause the library to
                    // copy all characters in s starting from pos

/* operations to modify strings */
s.insert(pos, args)
s.erase(pos, len)
s.assign(args)
s.append(args)
s.replace(range, args)

// args include: str; str, pos, len; cp, len; cp; n, c; b, e; initializer_list
```
![string_args](09-string-args.png)

### `string` Search Operations
```c++
s.find(args)                  // return the first pos of occurrence of args in s
s.refind(args)                // return the first pos of occurrence of args in s
                              // reversely match, last pos of reversed match in s
                              // reversely indexing starting at 0
s.find_first_of(args)
s.find_last_of(args)
s.find_first_not_of(args)
s.find_last_not_of(args)

// args must be one of c, pos; s2, pos; cp, pos; cp, pos, n
// pos defaults to 0 in the first three groups of args
```
If there is no match, the function returns a `static` member named `string::npos`. The library defines `npos` as a const `string::size_type` initialized with the value -1. Because `npos` is an unsigned type, this initializer means `npos` is equal to the largest possible size any `string` could have.

### The `compare` Functions
```c++
/* possible arguments to s.compare */
s2
pos1, n1, s2
pos1, n1, s2, pos2, n2
cp
pos1, n1, cp
pos1, n1, cp, n2
```

### Numeric Conversions
```c++
to_string(val);

stoi(s, p, b);
stol(s, p, b);
stoul(s, p, b);
stoll(s, p, b);
stoull(s, p, b);

stof(s, p);
stod(s, p);
stold(s, p);
```

## Container Adaptors
```c++
/* operations and types common to the container adaptors */
size_type
value_type
container_type
A a;
A a(c);
relational operators
a.empty()
a.size()
swap(a, b)
a.swap(b)
```
By default both `stack` and `queue` are implemented in terms of `deque`, and a `priority_queue` is implemented on a `vector`. We can override the default container type by naming a seqeuntail container as the second type argument when we create the adaptor.
```c++
stack<string, vector<string>> str_stk(svec);
```
There are constraints on which containers can be used for a given adaptor. All of the adaptors require the ability to add and remove elements. As a result, they cannot be built on an `array`. Similarly, we cannot use `forward_list`, because all of the adaptors require operations that add, remove, or access the last element in the container. A `stack` requires only `push_back`, `pop_back`, and `back` operations, so we can use any of the remaining container types for a `stack`. The `queue` adaptor requires `back`, `push_back`, `front`, and `push_front`, so it can be built on a `list` or `deque` but not on a `vector`. A `priority_queue` requires random access in addition to the `front`, `push_back`, and `pop_back` operations; it can be built on a `vector` or a `deque` but not on a `list`.
```c++
/* additional stack operations */
s.pop()
s.push(item)
s.emplace(args)
s.top()           // returns the top element on the stack

/* additional queue, priority_queue operations */
q.pop()
q.push(item)
q.emplace(args)

q.front()         // valid only for queue
q.back()          // valid only for queue
q.top()           // valid only for priority_queue
```
The library `queue` uses a first-in, first-out (FIFO) storage and retrieval policy. Objects entering the queue are placed in the back and objects leaving the queue are removed from the front. [more about priority_queue](https://blog.csdn.net/qq_41687938/article/details/117827166)