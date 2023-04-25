[TOC]
# Classes
2022.05.03
## Defining Abstract Data Type
Functions defined in class are implicitly `inline`. The operand of `&` should not be `const`. But the address of a constant object could be assigned to `this` of `const My_class *`.

The compiler processes classes in two steps —— the member declarations are compiled first, after which the member function bodies, if any, are processed.

### Constructors
Constructors have the same name as the class. Unlike other functions, constructors have no return type. Unlike other member functions, constructors may not be declared as `const`. When we create a `const` object of a class type, the object does assume its "constness" until after the constructor completes the object's initialization. Thus, constructors can write to `const` objects during their construction.

Class controls default initialization by defining a special constructor, known as the **default constructor**. The default constructor is one that takes no arguments. The compiler-generated constructor is known as the **synthesized default constructor**.

The most common reason that a class must define its own default constructor is that the compiler generates the default for us only if we do not define any other constructors for the class. Classes that have members of built-in or compound type usually should rely on the synthesized default constructor only if all such members have in-class initializers. Sometimes the compiler is unable to synthesize a default constructor. For example, if a class has a member that has a class type which has no default constructor, then the compiler can't initialize that member.
```c++
struct Sales_data{
    Sales_data() = default;
    Sales_data(const std::string &s): bookNo(s) { }
    Sales_data(const std::string &s, unsigned n, double p):
        bookNo(s), units_sold(n), revenue(p*n) { }
        /* constructor initializer list */
        /* followed by value in paratheses or curly braces */
    Sales_data(std::istream &is);
}

Sales_data::Sales_data(std::istream &is)
{
    /* members are still initialized before the 
       construct body is executed */ 
    read(is, *this); /* you can use it in construction */
}
```

## Access Control and Encapsulation
Members defined after a `private` specifier are accessible to the member functions of the class but not accessible to code that uses the class. The `private` sections encapsulate the implementation.

### Friends
A class can allow another class or function to access its non-public members by making that class or function a **friend**. A class make a function its friend by including a declaration for that function preceded by the keyword `friend`. Friend declaration may appear anywhere in the class. If we want users of the class to be able to call a friend function, then we must also declare the function separately from the friend declaration. While it works well without separate declarations on VS and g++.

## Additional Class Features
```c++
class Screen{
public:
    typedef std::string::size_type pos;
    // equivalent to
    // using pos = std::string::size_type;
    // must appear before use

    Screen() = default;
    Screen(pos ht, pos wd, char c): 
        height(ht), width(wd), contents(ht * wd, c) { }
    char get() const
        { return contents[cursor]; }
    inline char get(pos ht, pos wd) const;
    Screen &move(pos r, pos c);
    Screen &display(std::ostream &os)
        { do_display(os); return *this; }
    const Screen &display(std::ostream &os) const
        { do_display(os); return *this; }
private:
    void do_display(std::ostream &os) const
        { os << contents; }
    void some_member() const { ++access_ctr; }
private:
    pos cursor = 0;
    pos height = 0, width = 0;
    std::string contents;
    mutable size_t access_ctr; // may change in a const object
}

inline char Screen::get(pos ht, pos wd) const{
    ...
}

Screen &Screen::move(pos r, pos c){
    ...
}
```

When we provide an in-class initializer, we must do so following an equal mark or inside braces.

Every class defines a unique type. Two different classes define two different types even if they define the same members.

Classes and non-member functions need not have been declared before they are used in a friend declaration. When a name first appears in a friend declaration, that name is implicitly assumed to be part of the surrounding scope. However, the friend itself is not actually declared in that scope.
```c++
struct X{
    friend void f() { /* definition of f */ }
    X() { f(); } // error: no declaration for f
    void g();
    void h();
}
void X::g() { return f(); } // error: f has not been declared
void f();
void X::h() { return f(); } // ok
```

## Class Scope
Once the class name is seen, the remainder of the member definition, inlcuding the parameter list and the function body, is in the scope of the class. As a result, we refer to other class members without qualification. If the return type is defined in the class, an additional class qualifier is needed.

Ordinarily, an inner scope can redefine a name from an outer scope even if the name has already been used in the inner scope. However, in a class, if a member uses a name from an outer scope and the name is a type, then the class may not subsequently redefine that name.

A name used in the body of a member function will be looked for first inside the member function, then inside the class, and finally in the enclosing scopes of the function definition.

## Constructors Revisited
```c++
class Const_ref{
public:
    Const_ref(int li);
private:
    int i;
    const int ci;
    int &ri;
}

Const_ref::Const_ref(int li)
{              // assignments:
    i = li;    // ok
    ci = li;   // error: can't assign to a const
    ri = i;    // error: ri was never initialized
}

Const_ref::Const_ref(int ii): 
    i(ii), ci(ii), ri(i) /* initailizations */ { }
```

Members are initialized in the order in which they appear in the class definition instead of the constructor initializer list.

### Delegating Constructors
A **delegating constructor**, which is introduced in C++ 11, uses another constructor from its own class to perform its initialization. When a constructor delegates to another constructor, the constructor initializer list and function body of the delegated-to constructor are both executed.

### `explicit` Constructors
Only one class-type conversion is allowed. We can prevent the use of a constructor in a context that requires an implicit conversion by declaring the constructor as `explicit`. The `explicit` keyword is meaningful only on constructors that can be called with a single argument. Constructors that require more arguments are not used to perform an implicit conversion. The `explicit` keyword is used only on the constructor declaration inside the class. Explicit constructors can be used only for direct initialization instead of the copy form of initialization. Static cast is still useful for `explicit` constructors.
- The `string` constructor that takes a single parameter of type `const char *` is not `explicit`.
- The `vector` constructor that takes a size is `explicit`.

### Aggregate Classes
An **aggregate class**, known as a C-style struct, gives users direct access to its members and has special initialization syntax. We can initialize the data members of an aggregate class by providing a braced list of member initializers. As with initialization of array elements, if the list of initializers has fewer elements than the class has members, the trailing members are value initialized.

Literal Classes & `constexpr` Constructors

## Static Class Members
Note that `static` member functions are not bound to any object; they do not have a `this` pointer. As a result, `static` functions may not be declared as `const`, and we may not refer to `this` in the body of a `static` member. The keyword `static` appears only with the declaration inside the class body. Static data members are not initialized by the class' constructors. Moreover, in general, we may not initialize a static member inside the class. Instead, we must define and initialize each static data members outside the class body.
```c++
// define and initialize a static class member
// init_rate() is a private static member function
double Account::interest_rate = init_rate();
```
Ordinarily, class static members may not be initialized in the class body. However, we can provide in-class initializers for static members that have const integral type and must do so for static members that are constexprs of literal type. The initializers must be constant expressions. Even if a `const static` data member is initialized in the class body, that member ordinarily should be defined outside the class definition.

A static data member can have incomplete type. In particular, a static data member can have the same type as the class type of which it is a member. Another difference between static and ordinary members is that we can use a static member as a default argument.