# Object-Oriented Programming

2022.06.02

Object-oriented programming is based on three fundamental concepts: data abstraction, inheritance, and dynamic binding.

## OOP: An Overview

Classes related by **inheritance** form a hierarchy. Typically there is a **base class** at the root of the hierarchy, from which the other classes inherit, directly or indirectly. These inheriting classes are known as **derived classes**.

The base class defines as `virtual` those functions it expects its derived classes to define for themselves.

```c++
class Quote {
public:
    std::string isbn() const;
    virtual double net_price(std::size_t n) const;
};
```

A derived class must specify the class(es) from which it intends to inherit. It does so in a **class derivation list**, which is a colon followed by a comma-separated list of base classes each of which may have an optional access specifier:

```c++
class Bulk_quote : public Quote {
public:
    double net_price(std::size_t) const override;
};
```

### Inheritance

A derived class must include in its own class body a declaration of all the virtual functions it intends to define for itself. A derived class may include the `virtual` keyword on these functions but is not required to do so. The new standard lets a derived class explicitly note that it intends a member function to override a virtual that it inherits. It does so by specifying `override` after its parameter list.

### Dynamic Binding

```c++
// calculate and print the price for the given number of copies, applying any discounts
double print_total(ostream &os, const Quote &item, size_t n)
{
    // depending on the type of the object bound to the item parameter
    // calls either Quote::net_price or Bulk_quote::net_price
    double ret = item.net_price(n);
    os << "ISBN: " << item.isbn() // calls Quote::isbn
        << " # sold: " << n << " total due: " << ret << endl;
    return ret;
}

// basic has type Quote; bulk has type Bulk_quote
print_total(cout, basic, 20); // calls Quote version of net_price
print_total(cout, bulk, 20); // calls Bulk_quote version of net_price
```

Because the decision as to which version to run depends on the type of the argument, that decision can't be made until run time. Therefore, dynamic binding is sometimes known as **run-time binding**.

Note: In C++, dynamic binding happens when a virtual function is called through a reference (or a pointer) to a base class.

## Defining Base and Derived Classes

```c++
class Quote {
public:
    Quote() = default; // = default see § 7.1.4 (p. 264)
    Quote(const std::string &book, double sales_price):
        bookNo(book), price(sales_price) { }
    std::string isbn() const { return bookNo; }
    // returns the total sales price for the specified number of items
    // derived classes will override and apply different discount algorithms
    virtual double net_price(std::size_t n) const { return n * price; }
    virtual ~Quote() = default; // dynamic binding for the destructor`
private:
    std::string bookNo; // ISBN number of this item
protected:
    double price = 0.0; // normal, undiscounted price
};
```

Base classes ordinarily should define a virtual destructor. Virtual destructors are needed even if they do no work.

A base class specifies that a member function should be dynamically bound by preceding its declaration with the keyword `virtual`. Any nonstatic member function, other than a constructor, may be virtual. The virtual keyword appears only on the declaration inside the class and may not be used on a function definition that appears outside the class body. A function that is declared as virtual in the base class is implicitly virtual in the derived classes as well.

### Access Control and Inheritance

Like any other code that uses the base class, a derived class may access the public members of its base class but may not access the private members. However, sometimes a base class has members that it wants to let its derived classes use while still prohibiting access to those same members by other users. We specify such members after a `protected` access specifier.

```c++
class Bulk_quote : public Quote { // Bulk_quote inherits from Quote
    Bulk_quote() = default;
    Bulk_quote(const std::string&, double, std::size_t, double);
    // overrides the base version in order to implement the bulk purchase discount policy
    double net_price(std::size_t) const override;
private:
    std::size_t min_qty = 0; // minimum purchase for the discount to apply
    double discount = 0.0; // fractional discount to apply
};
```

When the derivation is `public`, the `public` members of the base class become part of the interface of the derived class as well. In addition, we can bind an object of a publicly derived type to a pointer or reference to the base type.

A derived object contains multiple parts: a subobject containing the (nonstatic) members defined in the derived class itself, plus subobjects corresponding to each base class from which the derived class inherits. The base and derived parts of an object are not guaranteed to be stored contiguously.

```c++
Quote item;         // object of base type
Bulk_quote bulk;    // object of derived type
Quote *p = &item;   // p points to a Quote object
p = &bulk;          // p points to the Quote part of bulk
Quote &r = bulk;    // r bound to the Quote part of bulk
```
This conversion is often referred to as the **derived-to-base conversion**. The fact that the derived-to-base conversion is implicit means that we can use an object of derived type or a reference to a derived type when a reference to the base type is required. Similarly, we can use a pointer to a derived type where a pointer to the base type is required.

Although a derived object contains members that it inherits from its base, it cannot directly initialize those members. Like any other code that creates an object of the base-class type, a derived class must use a base-class constructor to initialize its base-class part.

The scope of a derived class is nested inside the scope of its base class. As a result, there is no distinction between how a member of the derived class uses members defined in its own class and how it uses members defined in its base.

Under the new standard, we can prevent a class from being used as a base by following the class name with `final`.

There is no implicit conversion from base to derived and no conversion between objects.

The **static type** of an expression is always known at compile time—it is the type with which a variable is declared or that an expression yields. The **dynamic type** is the type of the object in memory that the variable or expression represents. The dynamic type may not be known until run time.

## Virtual Functions

It is crucial to understand that dynamic binding happens only when a virtual function is called through a pointer or a reference. Because we don’t know which version of a function is called until run time, virtual functions must always be defined.

When a derived class overrides a virtual function, it may, but is not required to, repeat the `virtual` keyword. Once a function is declared as `virtual`, it remains virtual in all the derived classes.

A derived-class function that overrides an inherited virtual function must have exactly the same parameter type(s) as the base-class function that it overrides. With one exception, the return type of a virtual in the derived class also must match the return type of the function from the base class. The exception applies to virtuals that return a reference (or pointer) to types that are themselves related by inheritance. That is, if `D` is derived from `B`, then a base class virtual can return a `B*` and the version in the derived can return a `D*`. However, such return types require that the derived-to-base conversion from `D` to `B` is accessible.

We can designate a function as `final`. Any attempt to override a function that has been defined as `final` will be flagged as an error.

Like any other function, a virtual function can have default arguments. If a call uses a default argument, the value that is used is the one defined by the static type through which the function is called. Best Practices: Virtual functions that have default arguments should use the same argument values in the base and derived classes.

We can use the scope operator to force the call to use a particular version of virtual.

```c++
// calls the version from the base class regardless of the dynamic type of baseP
double undiscounted = baseP->Quote::net_price(42);
```