# Dynamic Memory
2022.05.21

Dynamically allocated objects have a lifetime that is independent of where they are created; they exist until they are explicitly freed. Smart pointers ensure that the objects to which they point are automatically freed when it is appropriate to do so.

## Dynamic Memory and Smart Pointers
`new`, which allocates, and optionally initializes, an object in dynamic memory and returns a pointer to that object; and `delete`, which takes a pointer to a dynamic object, destroys that object, and frees the associated memory.

The new library defines two kinds of smart pointers that differ in how they manage their underlying pointers: `shared_ptr`, which allows multiple pointers to refer to the same object, and `unique_ptr`, which “owns” the object to which it points. The library also defines a companion class named `weak_ptr` that is a weak reference to an object managed by a `shared_ptr`. All three are defined in the `memory` header.

### The `shared_ptr` Class
```c++
shared_ptr<string> p1;
shared_ptr<vector<int>> p2;
```
A default initialized smart pointer holds a null pointer.
```c++
if (p1 && p1->empty()){
    *p1 = "hi";
}
```
```c++
/* operations common to shared_ptr and unique_ptr */
shared_ptr<T> sp
unique_ptr<T> up
p                   // use p as a condition
*p
p->mem
p.get()             // returns the pointer in p
swap(p, q)
p.swap(q)

/* operations specific to shared_ptr */
makr_shared<T>(args)    // Returns a shared_ptr pointing to a dynamically allocated
                        // object of type T. Uses args to initialize that object.
shared_ptr<T> p(q)      // p is a copy of the shared_ptr q; increments the count in q
p = q
p.unique()              // ture if use_count() is 1
p.use_count()           // slow, used to debug
```
The destructor for `shared_ptr` decrements the reference count of the object to which that `shared_ptr` points. If the count goes to zero, the `shared_ptr` destructor destroys the object to which the `shared_ptr` points and frees the memory used by that object.

### Managing Memory Directly
By default, dynamically allocated objects are default initialized, which means that objects of built-in or compound type have undefined value. We can also value initialize a dynamically allocated object by following the type name with a pair of empty parentheses. It is legal to use `new` to allocate `const` objects.

When we provide an initializer inside parentheses, we can use `auto` to deduce the type of the object we want to allocate from that initializer. However, because the compiler uses the initializer’s type to deduce the type to allocate, we can use `auto` only with a single initializer inside parentheses.

By default, if `new` is unable to allocate the requested storage, it throws an exception of type `bad_alloc`. We can prevent `new` from throwing an exception by using a different form of `new`:
```c++
int *p = new int; // if allocation fails, new throws bad_alloc
int *p2 = new (nothrow) int; // if allocation fails, new returns a null pointer
                             // a placement new expression
```
Both `bad_alloc` and `nothrow` are defined in the `new` header.

The pointer we pass to `delete` must either point to dynamically allocated memory or be a null pointer. Deleting a pointer to memory that was not allocated by `new`, or deleting the same pointer value more than once, is undefined.

### Using `shared_ptr` with `new`
```c++
shared_ptr<T> p(q)      // explicit, direct initialization needed
shared_ptr<T> p(u)      // makes unique_ptr u null
shared_ptr<T> p(q, d)
shared_ptr<T> p(p2, d)

p.reset()
p.reset(q)
p.reset(q, d)
```
```c++
shared_ptr<int> p1 = new int(1024);     // error: must use direct initialization
shared_ptr<int> p2(new int(1024));      // ok
shared_ptr<int> clone(int p){
    return new int(p); // error: implicit conversion to shared_ptr<int>
}
```
By default, a pointer used to initialize a smart pointer must point to dynamic memory because, by default, smart pointers use `delete` to free the associated object. We can bind smart pointers to pointers to other kinds of resources. However, to do so, we must supply our own operation to use in place of delete.

When we bind a `shared_ptr` to a plain pointer, we give responsibility for that memory to that `shared_ptr`. Once we give `shared_ptr` responsibility for a pointer, we should no longer use a built-in pointer to access the memory to which the `shared_ptr` now points.

Use `get` only to pass access to the pointer to code that you know will not `delete` the pointer. In particular, never use `get` to initialize or assign to another smart pointer.

### Smart Pointers and Exceptions
When a function is exited, whether through normal processing or due to an exception, all the local objects are destroyed. When we use a smart pointer, the smart pointer class ensures that memory is freed when it is no longer needed even if the block is exited prematurely.

### `unique_ptr`
```c++
unique_ptr<T> u1
unique_ptr<T,D> u2
unique_ptr<T,D> u(d)

u = nullptr         // deletes the object to which u points; makes u null
u.release()         // relinquishes control of the pointer u had held
                    // returns the pointer u had held and makes u null
u.reset()
u.reset(nullptr)
u.reset(q)
```
```c++
unique_ptr<int> clone(int p) {
    // ok: explicitly create a unique_ptr<int> from int*
    return unique_ptr<int>(new int(p));
}
unique_ptr<int> clone(int p) {
    unique_ptr<int> ret(new int (p));
    // . . .
    return ret;
}
```
In both cases, the compiler knows that the object being returned is about to be destroyed. In such cases, the compiler does a special kind of “copy” which we’ll discuss in Chapter 13.

### `weak_ptr`
```c++
weak_ptr<T> w
weak_ptr<T> w(sp)
w = p               // p can be a weak_ptr or a shared_ptr
w.use_count()
w.expired()         // true if use_count is 0
w.lock()            // if expired is true, returns a null shared_ptr
                    // else returns a shared_ptr to the object to which w points
// To access the object, we must call lock
```

## Dynamic Arrays
### `new` and Arrays (Not Recommended)
```c++
int *p = new int[get_size()];

int *pia = new int[10];     // default initialized
int *pia2 = new int[10]();  // value initialized
int *pia3 = new int[10]{4, 1, 4};
```
It is legal to dynamically allocate an empty array. This pointer acts as the off-the-end pointer for a zero-element array.
```c++
delete pa;      // undefined
delete [] pa;
```
Elements in an array are destroyed in reverse order.
```c++
unique_ptr<T[]> u
unique_ptr<T[]> u(p)
u[i]    // no dot or arrow operator
```
```c++
shared_ptr<int> sp(new int[10], [](int *p) { delete[] p; });
sp.reset(); // uses the lambda we supplied that uses delete[] to free the array
```
There is no subscript operator for `shared_ptr`, and the smart pointer types do not support pointer arithmetic.

### The `allocator` Class
```c++
/* allocator is defined in `memory` header */
allocator<T> a
a.allocate(n)
a.deallocate(p, n)
a.construct(p, args)
a.destroy(p)

uninitialized_copy(b, e, b2)
uninitialized_copy_n(b, n, b2)
uninitialized_fill(b, e, t)
uninitialized_fill_n(b, n, t)
```

## References
- [Where do vectors store elements](https://stackoverflow.com/questions/8036474)
- [Caution with shared_ptr](https://zhuanlan.zhihu.com/p/448376125)