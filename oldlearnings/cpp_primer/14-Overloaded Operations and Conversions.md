# Overloaded Operations and Conversions

2022.06.01

Overloaded operators are functions with special names: the keyword `operator` followed by the symbol for the operator being defined. Except for the overloaded function-call operator, `operator()`, an overloaded operator may not have default arguments.

If an operator function is a member function, the first (left-hand) operand is bound to the implicit this pointer. Because the first operand is implicitly bound to this, a member operator function has one less (explicit) parameter than the operator has operands.

An operator function must either be a member of a class or have at least one parameter of class type:

```c++
// error: cannot redefine the built-in operator for ints
int operator+(int, int);
```

```
Operators That May Be Overloaded
+       -       *       /       %       ^
&       |       ~       !       ,       =
<       >       <=      >=      ++      --
<<      >>      ==      !=      &&      ||
+=      -=      /=      %=      ^=      &=
|=      *=      <<=     >>=     []      ()
->      ->*     new     new []  delete  delete []

Operators That Cannot Be Overloaded
::      .*      .       ?:
```

We can overload only existing operators and cannot invent new operator symbols. For example, we cannot define `operator**` to provide exponentiation.

Recall that a few operators guarantee the order in which operands are evaluated. Because using an overloaded operator is really a function call, these guarantees do not apply to overloaded operators. In particular, the operand-evaluation guarantees of the logical AND, logical OR, and comma operators are not preserved. Moreover, overloaded versions of `&&` or `||` operators do not preserve short-circuit evaluation properties of the built-in operators. Both operands are always evaluated. Ordinarily, the comma, address-of, logical AND , and logical OR operators should not be overloaded.

The following guidelines can be of help in deciding whether to make an operator a member or an ordinary nonmember function:
+ The assignment (`=`), subscript (`[]`), call (`()`), and member access arrow (`->`) operators must be defined as members.
+ The compound-assignment operators ordinarily ought to be members. However, unlike assignment, they are not required to be members.
+ Operators that change the state of their object or that are closely tied to their given type—such as increment, decrement, and dereference—usually should be members.
+ Symmetric operators—those that might convert either operand, such as the arithmetic, equality, relational, and bitwise operators—usually should be defined as ordinary nonmember functions.

## Input and Output Operators

## Arithmetic and Relational Operator

## Assignment Operators

In addition to the copy- and move-assignment operators that assign one object of the class type to another object of the same type, a class can define additional assignment operators that allow other types as the right-hand operand.

```c++
class StrVec {
public:
    StrVec &operator=(std::initializer_list<std::string>);
    // other members as in § 13.5 (p. 526)
};
```

## Subscript Operator

```c++
class StrVec {
public:
    std::string& operator[](std::size_t n)
    { return elements[n]; }
    const std::string& operator[](std::size_t n) const
    { return elements[n]; }
    // other members as in § 13.5 (p. 526)
private:
    std::string *elements; // pointer to the first element in the array
};
```

## Increment and Decrement Operators

```c++
class StrBlobPtr {
public:
    // increment and decrement
    StrBlobPtr& operator++(); // prefix operators
    StrBlobPtr& operator--();
    StrBlobPtr operator++(int); // postfix operators
    StrBlobPtr operator--(int);
};

// prefix: return a reference to the incremented/decremented object
StrBlobPtr& StrBlobPtr::operator++()
{
    // if curr already points past the end of the container, can't increment it
    check(curr, "increment past end of StrBlobPtr");
    ++curr; // advance the current state
    return *this;
}

// postfix: increment/decrement the object but return the unchanged value
StrBlobPtr StrBlobPtr::operator++(int)
{
    // no check needed here; the call to prefix increment will do the check
    StrBlobPtr ret = *this; // save the current value
    ++*this; // advance one element; prefix ++ checks the increment
    return ret; // return the saved state
}
```

## Member Access Operators

```c++
class StrBlobPtr {
public:
    std::string& operator*() const
    { 
        auto p = check(curr, "dereference past end");
        return (*p)[curr]; // (*p) is the vector to which this object points
    }

    std::string* operator->() const
    { 
        // delegate the real work to the dereference operator
        return & this->operator*();
    }
    // other members
};
```

When we overload arrow, we change the object from which arrow fetches the specified member. We cannot change the fact that arrow fetches a member. When we write `point->mem`, `point` must be a pointer to a class object or it must be an object of a class with an overloaded `operator->`. Depending on the type of `point`, writing `point->mem` is equivalent to
```c++
(*point).mem; // point is a built-in pointer type
point.operator()->mem; // point is an object of class type
```

The overloaded arrow operator must return either a pointer to a class type or an object of a class type that defines its own operator arrow.

## Function-Call Operator

Classes that overload the call operator allow objects of its type to be used as if they were a function. Because such classes can also store state, they can be more flexible than ordinary functions.

```c++
class PrintString {
public:
    PrintString(ostream &o = cout, char c = ' '): os(o), sep(c) { }
    void operator()(const string &s) const { os << s << sep;}
private:
    ostream &os; // stream on which to write
    char sep; // character to print after each output
};

PrintString printer; // uses the defaults; prints to cout
printer(s); // prints s followed by a space on cout
PrintString errors(cerr, '\n');
errors(s); // prints s followed by a newline on cerr
```

Objects of classes that define the call operator are referred to as **function objects**. Such objects “act like functions” because we can call them.

Function objects are most often used as arguments to the generic algorithms.
```c++
for_each(vs.begin(), vs.end(), PrintString(cerr, '\n'));
```

### Lambdas Are Function Objects

When we write a lambda, the compiler translates that expression into an unnamed object of an unnamed class. The classes generated from a lambda contain an overloaded function-call operator.

```c++
stable_sort(words.begin(), words.end(), 
    [](const string &a, const string &b) { return a.size() < b.size();});
```
acts like an unnamed object of a class that would look something like
```c++
class ShorterString {
public:
    bool operator()(const string &s1, const string &s2) const
        { return s1.size() < s2.size(); }
};
stable_sort(words.begin(), words.end(), ShorterString());
```

Varibles in the capture list appear as members of the class. By default, lambdas may not change their captured variables. As a result, by default, the function-call operator in a class generated from a lambda is a `const` member function. If the lambda is declared as `mutable`, then the call operator is not `const`.

Classes generated from a lambda expression have a deleted default constructor, deleted assignment operators, and a default destructor. Whether the class has a defaulted or deleted copy/move constructor depends in the usual ways on the types of the captured data members.

```c++
/* library funtion classes / objects defined in functional header */
// Arithmetic       Relational              Logical
plus<Type>          equal_to<Type>          logical_and<Type>
minus<Type>         not_equal_to<Type>      logical_or<Type>
multiplies<Type>    greater<Type>           logical_not<Type>
divides<Type>       greater_equal<Type>
modolus<Type>       less<Type>
negate<Type>        less_equal<Type>
```

```c++
// passes a temporary function object that applies the < operator to two strings
sort(svec.begin(), svec.end(), greater<string>());
```

### Callable Objects and `function`

C++ has several kinds of callable objects: functions and pointers to functions, lambdas, objects created by `bind`, and classes that overload the function-call operator. Like any other object, a callable object has a type. For example, each lambda has its own unique (unnamed) class type. Function and function-pointer types vary by their return type and argument types, and so on.

However, two callable objects with different types may share the same **call signature**. The call signature specifies the type returned by a call to the object and the argument type(s) that must be passed in the call. A call signature corresponds to a function type. For example: `int(int, int)` is a function type that takes two ints and returns an int.

```c++
// ordinary function
int add(int i, int j) { return i + j; }
// lambda, which generates an unnamed function-object class
auto mod = [](int i, int j) { return i % j; };
// function-object class
struct div {
    int operator()(int denominator, int divisor) {
        return denominator / divisor;
    }
};
```
Each of these callables applies an arithmetic operation to its parameters. Even though each has a distinct type, they all share the same call signature.

We might want to use these callables to build a simple desk calculator. To do so, we’d want to define a **function table** to store “pointers” to these callables. When the program needs to execute a particular operation, it will look in the table to find which function to call.

If all our functions are freestanding functions, we may implement a function table using a map of a type like `map<string, int(*)(int,int)>`. But there would be a problem when there are also lambdas and objects of function classes. We can solve this problem using a new library type `function` defined in the `funtional` header. And the operations listed below are defined in the `function` header.

```c++
function<T> f;          // f is a null `function` object that can store callable objects
                        // with a call signature that is equivalent to the function type T
function<T> f(nullptr); // explicitly construct a null `function`
function<T> f(obj);     // stores a copy of the callable object obj in f
f                       // use f as a condition; true if f holds a callable object
f(args)                 // calls the object in f passing args

result_type
argument_type
first_argument_type
second_argument_type
```

```c++
function<int(int, int)> f1 = add; // function pointer
function<int(int, int)> f2 = div(); // object of a function-object class
function<int(int, int)> f3 = [](int i, int j) // lambda { return i * j; };
cout << f1(4,2) << endl; // prints 6
cout << f2(4,2) << endl; // prints 2
cout << f3(4,2) << endl; // prints 8

map<string, function<int(int, int)>> binops = {
    {"+", add}, // function pointer
    {"-", std::minus<int>()}, // library function object
    {"/", div()}, // user-defined function object
    {"*", [](int i, int j) { return i * j; }}, // unnamed lambda
    {"%", mod} // named lambda object
};
```

We cannot (directly) store the name of an overloaded function in an object of type `function`. One way to resolve the ambiguity is to store a function pointer instead of the name of the function.

## Overloading, Conversions, and Operators

A **conversion operator** is a special kind of member function that converts a value of a class type to a value of some other type. A conversion function typically has the general form:
```c++
operator type() const;
```
where type represents a type. Conversion operators can be defined for any type (other than void) that can be a function return type. Conversions to an array or a function type are not permitted. Conversions to pointer types—both data and function pointers—and to reference types are allowed.

Conversion operators have no explicitly stated return type and no parameters, and they must be defined as member functions. Conversion operations ordinarily should not change the object they are converting. As a result, conversion operators usually should be defined as `const` members.

```c++
class SmallInt {
public:
    operator int(int = 0) const; // error: parameter list
    operator int*() const { return 42; } // error: 42 is not a pointer
};
```

In practice, classes rarely provide conversion operators. And a rule of thumb is that it is not uncommon for classes to define conversions to `bool`. Under earlier versions of the standard, classes that wanted to define a conversion to bool faced a problem: Because bool is an arithmetic type, a class-type object that is converted to bool can be used in any context where an arithmetic type is expected. Such conversions can happen in surprising ways. In particular, if istream had a conversion to bool, the following code would compile.

To prevent such problems, the new standard introduced explicit conversion operators:
```c++
class SmallInt {
public:
    // the compiler won't automatically apply this conversion
    explicit operator int() const { return val; }
    // other members
};
```
As with an explicit constructor, the compiler won’t (generally) use an explicit conversion operator for implicit conversions. The exception is that the compiler will apply an `explicit` conversion to an expression used as a condition.