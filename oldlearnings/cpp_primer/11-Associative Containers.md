# Associative Containers
2022.05.24

Elements in an associative container are stored and retrieved by a key. By contrast, elements in a sequential container are stored and accessed sequentially by their position in the container.

```c++
/* associative container types */
map                     // defined in map header
set                     // defined in set header
multimap                // defined in map header, a key can appear multiple times
multiset                // defined in set header, a key can appear multiple times
unordered_map           // defined in unordered_map header
unordered_set           // defined in unordered_set header
unordered_multimap      // defined in unordered_map header
unordered_multiset      // defined in unordered_set header
```
A default map or set should be ordered, in which a key may appear only once.

## Using an Associative Container
The `map` type is often referred to as an **associative array**.

## Overview of the Associative Containers
The associative containers do not support the sequential-container position-specific operations, such as `push_front` or `back`. Moreover, the associative containers do not support the constructors or insert operations that take an element value and a count.
```c++
/* list initialization of set and map */
set<string> exclude = {"the", "but", "and", "or", "an", "a"};
map<string, string> authors = { {"Joyce", "James"}, {"Austen", "Jane"}};
```

### Key Types for Ordered Containers
Just as we can provide our own comparison operation to an algorithm, we can also supply our own operation to use in place of the < operator on keys. The specified operation must define a strict weak ordering over the key type.
```c++
multiset<Sales_data, decltype(compareIsbn)*> bookstore(compareIsbn);
```

### The `pair` type
The `pair` type is defined in `utility` header.
```c++
pair<string, string> anon;
pair<string, string> author{"James", "Joyce"};
```
```c++
pair<T1, T2> p;
pair<T1, T2> p(v1, v2);
pair<T1, T2> p = {v1, v2}; // equals to p(v1, v2)
make_pair(v1, v2);         // infer the pair type
p.first
p.second
p1 relop/==/!= p2
```

## Operations on Associative Container
```c++
/* associative container additional type aliases */
key_type
mapped_type     // map types only
value_type      // for sets, same as key_type
                // for maps, pair<const key_type, mapped_type>
```
```c++
set<string>::value_type v1;         // v1 is a string
set<string>::key_type v2;           // v2 is a string
map<string, int>::value_type v3;    // v3 is a pair<const string, int>
map<string, int>::key_type v4;      // v4 is a string
map<string, int>::mapped_type v5;   // v5 is an int
```
Although the `set` types define both the `iterator` and `const_iterator` types, both types of iterators give us read-only access to the elements in the set.

### Adding Elements

```c++
/* associative container insert operations */
c.insert(v)			// returns a pair or an iterator
c.emplace(args)		// returns a pair or an iterator
c.insert(b, e)		// returns void
c.insert(il)		// returns void
c.insert(p, v)		// p used as a hint if it is an iterator to the position before 
					// which the new element will be inserted
c.emplace(p, v)
```

For the containers that have unique keys, the versions of `insert` and `emplace` that add a single element return a pair that lets us know whether the insertion happened. The first member of the pair is an iterator to the element with the given key; the second is a bool indicating whether that element was inserted, or was already there. If the key is already in the container, then `insert` does nothing, and the bool portion of the return value is false. If the key isn’t present, then the element is inserted and the bool is true.

For the containers that allow multiple keys, the insert operation that takes a single element returns an iterator to the new  element. There is no need to return a bool, because insert always adds a new element in these types.

### Erasing Elements

```c++
/* removing elements from an associative container */
c.erase(k)			// k for key, returns the number of elements removed
c.erase(p)			// p as an iterator, returns the iterator afeter the removed element
c.erase(b, e)		// returns e
```

### Subscripting a Map

The `map` and `unordered_map` containers, which are not `const`, provide the subscript operator and a corresponding at function. The `set` types do not support subscripting because there is no “value” associated with a key in a `set`. The elements are themselves keys, so the operation of “fetching the value associated with a key” is meaningless. We cannot subscript a `multimap` or an `unordered_multimap` because there may be more than one value associated with a given key.

```c++
/* a new way to insert an element into a map */
map<string, size_t> word_count;
word_count["Anna"] = 1;
```

Using a key that is not already present adds an element with that key to the `map`.

### Accessing Elements

```c++
/* operations to find elements in an associative container */
c.find(k)			// returns an iterator to the first element with key k
					// or off-the-end iterator if k is not in the container
c.count(k)			// returns the numbers of elements with key k
c.lower_bound(k)	// returns an iterator to the first element with key not less than k
c.upper_bound(k)	// returns an iterator to the first element with key greater than k
c.equal_range(k)	// returns a pair of iterators denoting the elements with k
					// if k is not present, both members are c.end()
```

## The Unordered Containers

Rather than using a comparison operation to organize their elements, unordered associative containers use a hash function and the key type’s `==` operator.

### Managing the Bucket

The unordered containers are organized as a collection of buckets, each of which holds zero or more elements. These containers use a hash function to map elements to buckets.

```c++
/* unordered container management operations */
// Bucket Iterface
c.bucket_count()
c.max_bucket_count()
c.bucket_size(n)
c.bucket(k)

// Bucket Iteration
local_iterator			// iterator type that can access elements in a bucket
const_local_iterator
c.begin(n), c.end(n)
c.cbegin(n), c.cend(n)

// Hash Policy
c.load_factor()
c.max_load_factor()
c.rehash(n)				// reorganize so that bucket_count >= n and > size/max_load_factor
c.reserve(n)			// reorganize so that c can hold n elements without rehash
```

```c++
size_t hasher(const Sales_data &sd)
{
	return hash<string>()(sd.isbn());
}

bool eqOp(const Sales_data &lhs, const Sales_data &rhs)
{
	return lhs.isbn() == rhs.isbn();
}

int main()
{
    using SD_multiset = unordered_multiset<Sales_data, decltype(hasher)*, decltype(eqOp)*>;
    // arguments are the bucket size and pointers to the hash function and equality operator
    SD_multiset bookstore(42, hasher, eqOp);
}
```

