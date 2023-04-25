# Copy Control

2022.05.30

In this chapter, we'll learn how classes can control what happens when objects of the class type are copied, assigned, moved, or destroyed. Classes control these actions through special member functions: the copy constructor, move constructor, copy-assignment operator, move-assignment operator, and destructor.

The copy and move constructors define what happens when an object is initialized from another object of the same type. The copy- and move-assignment operators define what happens when we assign an object of a class type to another object of that same class type. The destructor defines what happens when an object of the type ceases to exist. Collectively, we’ll refer to these operations as **copy control**.

If a class does not define all of the copy-control members, the compiler automatically defines the missing operations.

## Copy, Assign, and Destroy

### The Copy Constructor

A constructor is the copy constructor if its first parameter is a reference to the class type and any additional parameters have default values:

```c++
class Foo {
    public:
    Foo(); 				// default constructor
    Foo(const Foo &); 	// copy constructor
    // ...
};
```

The first parameter is almost always a reference to const, although we can define the copy constructor to take a reference to nonconst. The copy constructor is used implicitly in several circumstances. Hence, the copy constructor usually should not be
`explicit`.

#### The Synthesized Copy Constructor

When we do not define a copy constructor  for a class, the compiler synthesizes one for us. The synthesized copy constructor for some classes prevents us from copying objects of that class type. Otherwise, the synthesized copy constructor **memberwise copies** the members of its argument into the object being created.

Members of class type are copied by the copy constructor for that class; members of built-in type are copied directly. Although we cannot directly copy an array, the synthesized copy constructor copies members of array type by copying each element.

#### Copy Initialization

```c++
/* the difference between direct initialization and copy initialization */
string dots(10, '.');			// direct initialization
string s(dots);					// direct initialization
string s2 = dots;				// copy initialization
string null_book = "9999999";	// copy initialization
string nines = string(100, '9');// copy initialization
```

When we use direct initialization, we are asking the compiler to use ordinary function matching to select the constructor that best matches the arguments we provide. When we use copy initialization, we are asking the compiler to copy the right-hand operand into the object being created, converting that operand if necessary.

Copy initialization ordinarily uses the copy constructor. However, if a class has a move constructor, then copy initialization sometimes uses the move constructor instead of the copy constructor.

Copy initialization happens not only when we define variables using an =, but also when we

+ Pass an object as an argument to a parameter of nonreference type
+ Return an object from a function that has a nonreference return type
+ Brace initialize the elements in an array or the members of an aggregate class

Some class types also use copy initialization for the objects they allocate. For example, the library containers copy initialize their elements when we initialize the container, or when we call an `insert` or `push` member. By contrast, elements created by an `emplace` member are direct initialized.

The fact that the copy constructor is used to initialize nonreference parameters of class type explains why the copy constructor's own parameter must be a reference. If that parameter were not a reference, then the call would never succeed.

#### Constraints on Copy Initialization

Whether we use copy or direct initialization matters if we use an initializer that requires conversion by an `explicit` constructor.

```c++
/* If the copy constructor is explicit, the copied varible 
   in copy initialization must be of exactly the class type */
vector<int> v1(10); 	// ok: direct initialization
vector<int> v2 = 10; 	// error: constructor that takes a size is explicit
void f(vector<int>); 	// f's parameter is copy initialized
f(10); 					// error: can't use an explicit constructor to copy an argument
f(vector<int>(10)); 	// ok: directly construct a temporary vector from an int
```

#### The Compiler Can Bypass the Copy Constructor

During copy initialization, the compiler is permitted (but not obligated) to skip the copy/move constructor and create the object directly. That is, the compiler is permitted to rewrite

```c++
string null_book = "9-999-99999-9"; // copy initialization
```

into

```c++
string null_book("9-999-99999-9"); // compiler omits the copy constructor
```

However, even if the compiler omits the call to the copy/move constructor, the copy/move constructor must exist and must be accessible (e.g., not `private`) at that point in the program.

### The Copy-Assignment Operator

**Overloaded operators** are functions that have the name `operator` followed by the symbol for the operator being defined. Hence, the assignment operator is a function named `operator=`.

```:sa:
class Foo {
public:
    Foo &operator=(const Foo &); // assignment operator
    // ...
};
```

### The Destructor

The destructor is a member function with the name of the class prefixed by a tilde(~). It has no return value and takes no parameters:

```c++
class Foo {
public:
    ~Foo(); // destructor
    // ...
};
```

In a constructor, members are initialized before the function body is executed, and members are initialized in the same order as they appear in the class. In a destructor, the function body is executed first and then the members are destroyed. Members are destroyed in reverse order from the order in which they were initialized.

In a destructor, there is nothing akin to the constructor initializer list to control how members are destroyed; the destruction part is implicit. What happens when a member is destroyed depends on the type of the member. Members of class type are destroyed by running the member’s own destructor. The built-in types do not have destructors, so nothing is done to destroy members of built-in type.

The destructor is not run when a reference or a pointer to an object goes out of scope.

### The Rule of Three/Five

### Using `=default`

```c++
class Sales_data {
public:
    // copy control; use defaults
    Sales_data() = default;
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &);
    ~Sales_data() = default;
    // other members
};
Sales_data &Sales_data::operator=(const Sales_data&) = default;
```

### Preventing Copies

Under the new standard, we can prevent copies by defining the copy constructor and copy-assignment operator as **deleted functions**. Unlike `= default`, `= delete` must appear on the first declaration of a deleted function.

```c++
struct NoCopy {
    NoCopy() = default; // use the synthesized default constructor
    NoCopy(const NoCopy&) = delete; // no copy
    NoCopy &operator=(const NoCopy&) = delete; // no assignment
    ~NoCopy() = default; // use the synthesized destructor
    // other members
};
```

Also unlike `= default`, we can specify `= delete` on any function (we can use `= default` only on the default constructor or a copy-control member that the compiler can synthesize). Although the primary use of deleted functions is to suppress the copy-control members, deleted functions are sometimes also useful when we want to guide the function-matching process.

For some classes, the compiler defines these synthesized members as deleted functions:

+ The synthesized destructor is defined as deleted if the class has a member whose own destructor is deleted or is inaccessible (e.g., private).
+ The synthesized copy constructor is defined as deleted if the class has a member whose own copy constructor is deleted or inaccessible. It is also deleted if the class has a member with a deleted or inaccessible destructor.
+ The synthesized copy-assignment operator is defined as deleted if a member has a deleted or inaccessible copy-assignment operator, or if the class has a const or reference member.
+ The synthesized default constructor is defined as deleted if the class has a member with a deleted or inaccessible destructor; or has a reference member that does not have an in-class initializer; or has a const member whose type does not explicitly define a default constructor and that member does not have an in-class initializer.

## Copy Control and Resource Management

### Classes That Act Like Values

```c++
HasPtr &HasPtr::operator=(const HasPtr &rhs)
{
    auto newp = new string(*rhs.ps); // copy the underlying string
    delete ps; // free the old memory
    ps = newp; // copy data from rhs into this object
    i = rhs.i;
    return *this; // return this object
}
```

There are two points to keep in mind when you write an assignment operator:

+ Assignment operators must work correctly if an object is assigned to itself.
+ Most assignment operators share work with the destructor and copy constructor.

### Classes That Act Like Pointers

Note: Use a reference counter member stored in dynamic memory.

## Swap

Defining `swap` is particularly important for classes that we plan to use with algorithms that reorder elements. If a class defines its own `swap`, then the algorithm uses that class-specific version. Otherwise, it uses the `swap` function defined by the library. Although, as usual, we don’t know how swap is implemented, conceptually it’s easy to see that swapping two objects involves a copy and two assignments.

```c++
class HasPtr {
    friend void swap(HasPtr&, HasPtr&);
    // other members
};

inline void swap(HasPtr &lhs, HasPtr &rhs)
{
    using std::swap;
    swap(lhs.ps, rhs.ps); // swap the pointers, not the string data
    swap(lhs.i, rhs.i); // swap the int members
}
```

### Using `swap` in Assignment Operators

Classes that define `swap` often use `swap` to define their assignment operator. These operators use a technique known as **copy and swap**. This technique swaps the left-hand operand with a copy of the right-hand operand:

```c++
// note rhs is passed by value, which means the HasPtr copy constructor
// copies the string in the right-hand operand into rhs
HasPtr& HasPtr::operator=(HasPtr rhs)
{
    // swap the contents of the left-hand operand with the local variable rhs
    swap(*this, rhs); // rhs now points to the memory this object had used
    return *this; // rhs is destroyed, which deletes the pointer in rhs
}
```

Assignment operators that use copy and swap are automatically exception safe and correctly handle self-assignment.

## Moving Objects

In some circumstances, an object is immediately destroyed after it is copied. In those cases, moving, rather than copying, the object can provide a significant performance boost. A second reason to move rather than copy occurs in classes such as the IO or `unique_ptr` classes. These classes have a resource (such as a pointer or an IO buffer) that may not be shared. Hence, objects of these types can’t be copied but can be moved. Under the new standard, we can use containers on types that cannot be copied so long as they can be moved.

### Rvalue References

An **rvalue reference** is a reference that must be bound to an rvalue. An rvalue reference is obtained by using `&&` rather than `&`. Rvalue references have the important property that they may be bound only to an object that is about to be destroyed. As a result, we are free to “move” resources from an rvalue reference to another object.

Recall that lvalue and rvalue are properties of an expression. Like any reference, an rvalue reference is just another name for an object.

```c++
int i = 42;
int &r = i; // ok: r refers to i
int &&rr = i; // error: cannot bind an rvalue reference to an lvalue
int &r2 = i * 42; // error: i * 42 is an rvalue
const int &r3 = i * 42; // ok: we can bind a reference to const to an rvalue
int &&rr2 = i * 42; // ok: bind rr2 to the result of the multiplication
```

Rvalue references refer to objects that are about to be destroyed. Hence, we can “steal” state from an object bound to an rvalue reference.

```c++
int &&rr1 = 42; // ok: literals are rvalues
int &&rr2 = rr1; // error: the expression rr1 is an lvalue!
int &&rr3 = std::move(rr1); // ok
```

Calling `move` tells the compiler that we have an lvalue that we want to treat as if it were an rvalue. It is essential to realize that the call to `move` promises that we do not intend to use `rr1` again except to assign to it or to destroy it. After a call to `move`, we cannot make any assumptions about the value of the moved-from object.

As we’ve seen, differently from how we use most names from the library, we do not provide a `using` declaration for `move`. We call `std::move` not `move`.

### Move Constructors and Move Assignment

```c++
StrVec::StrVec(StrVec &&s) noexcept // move won't throw any exceptions
// member initializers take over the resources in s
: elements(s.elements), first_free(s.first_free), cap(s.cap)
{
    // leave s in a state in which it is safe to run the destructor
    s.elements = s.first_free = s.cap = nullptr;
}
```

For now what’s important to know is that `noexcept` is a way for us to promise that a function does not throw any exceptions. We specify `noexcept` on a function after its parameter list. We must specify `noexcept` on both the declaration in the class header and on the definition if that definition appears outside the class.

The compiler will synthesize a move constructor or a move-assignment operator only if the class doesn’t define any of its own copy-control members and if every nonstatic data member of the class can be moved. The compiler can move members of built-in type. It can also move members of a class type if the member’s class has the corresponding move operation:

```c++
// the compiler will synthesize the move operations for X and hasX
struct X {
    int i; // built-in types can be moved
    std::string s; // string defines its own move operations
};
struct hasX {
	X mem; // X has synthesized move operations
};
X x, x2 = std::move(x); // uses the synthesized move constructor
hasX hx, hx2 = std::move(hx); // uses the synthesized move constructor
```

Unlike the copy operations, a move operation is never implicitly defined as a deleted function. However, if we explicitly ask the compiler to generate a move operation by using `= default`, and the compiler is unable to move all the members, then the move operation will be defined as deleted. 

```c++
// assume Y is a class that defines its own copy constructor but not a move constructor
struct hasY {
    hasY() = default;
    hasY(hasY&&) = default;
    Y mem; // hasY will have a deleted move constructor
};
hasY hy, hy2 = std::move(hy); // error: move constructor is deleted
```

Classes that define a move constructor or move-assignment operator must also define their own copy operations. Otherwise, those members are deleted by default.

The version of our `HasPtr` class that defined a **copy-and-swap** assignment operator is a good illustration of the interaction between function matching and move operations. If we add a move constructor to this class, it will effectively get a move assignment operator as well:

```c++
class HasPtr {
public:
    // added move constructor
    HasPtr(HasPtr &&p) noexcept : ps(p.ps), i(p.i) {p.ps = 0;}
    // assignment operator is both the move- and copy-assignment operator
    HasPtr& operator=(HasPtr rhs)
    	{ swap(*this, rhs); return *this; }
    // other members
};
```

### Rvalue References and Member Functions

Member functions other than constructors and assignment can benefit from providing both copy and move versions. Such move-enabled members typically use the same parameter pattern as the copy/move constructor and the assignment operators—one
version takes an lvalue reference to const, and the second takes an rvalue reference to nonconst.

```c++
void push_back(const X&); // copy: binds to any kind of X
void push_back(X&&); // move: binds only to modifiable rvalues of type X
```

```c++
void StrVec::push_back(const string& s)
{
    chk_n_alloc(); // ensure that there is room for another element
    // construct a copy of s in the element to which first_free points
    alloc.construct(first_free++, s);
}
void StrVec::push_back(string &&s)
{
    chk_n_alloc(); // reallocates the StrVec if necessary
    alloc.construct(first_free++, std::move(s));
}
```

### Rvalue and Lvalue Reference Member Functions

We indicate the lvalue/rvalue property of this in the same way that we define `const` member functions; we place a reference qualifier after the parameter list:

```c++
class Foo {
public:
    Foo &operator=(const Foo&) &; // may assign only to modifiable lvalues
    // other members of Foo
};
Foo &Foo::operator=(const Foo &rhs) &
{
    // do whatever is needed to assign rhs to this object
    return *this;
}
```

The reference qualifier can be either & or &&, indicating that `this` may point to an rvalue or lvalue, respectively. We may run a function qualified by & only on an lvalue and may run a function qualified by && only on an rvalue. A function can be both const and reference qualified. In such cases, the reference qualifier must follow the const qualifier:

```c++
class Foo {
public:
    Foo sorted() &&; // may run on modifiable rvalues
    Foo sorted() const &; // may run on any kind of Foo
    // other members of Foo
private:
	vector<int> data;
};

// this object is an rvalue, so we can sort in place
Foo Foo::sorted() &&
{
    sort(data.begin(), data.end());
    return *this;
}

// this object is either const or it is an lvalue; either way we can't sort in place
Foo Foo::sorted() const & {
    Foo ret(*this); // make a copy
    sort(ret.data.begin(), ret.data.end()); // sort the copy
    return ret; // return the copy
}
```

```c++
class Foo {
public:
    Foo sorted() &&;
    Foo sorted() const; // error: must have reference qualifier
    using Comp = bool(const int&, const int&);
    Foo sorted(Comp*); 		 // ok: different parameter list
    Foo sorted(Comp*) const; // ok: neither version is reference qualified
};
```

If a member function has a reference qualifier, all the versions of that member with the same parameter list must have reference qualifiers.