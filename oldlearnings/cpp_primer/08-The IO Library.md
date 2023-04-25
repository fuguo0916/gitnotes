# The IO Library
2022.05.17
## The IO classes
IO library types and headers:
|header|types|
|--|--|
|iostream|istream, ostream, iostream, wistream, ...|
|fstream|ifstream, ofstream, fstream, wifstream, ...|
|sstream|istringstream, ostringstream, stringstream, ...|

Because we can’t copy the IO types, functions that do IO typically pass and return the stream through references.

```c++
/* IO Library Condition State */
strm::iostate   // a machine-dependent integral type
strm::badbit    // strm::iostate value 4, system-level failure, unrecoverable
strm::failbit   // strm::iostate value 2, recoverable error
strm::eofbit    // strm::iostate value 1
strm::goodbit   // strm::iostate value 0
// when an EOF is encountered, both failbit and eofbit will be set

s.eof()         // true if eofbit set
s.bad()         // true if badbit set
s.fail()        // true if failbit or badbit set
s.good()        // true if none of the three bits set

s.clear()           // reset condition of s to a valid state
s.clear(flags)      // reset condition of s to flags
s.setstate(flags)   // adds specified condition(s) to s
s.rdstate()         // return iostate
```

There are several conditions that cause the buffer to be flushed:
- The program completes normally. All output buffers are flushed as part of the return from `main`.
- At some indeterminate time, the buffer can become full, in which case it will be flushed before writing the next value.
- We can flush the buffer explicitly using a manipulator such as `endl`.
- We can use the `unitbuf` manipulator to set the stream’s internal state to empty the buffer after each output operation. By default, `unitbuf` is set for cerr, so that writes to cerr are flushed immediately.
  - An output stream might be tied to another stream. In this case, the buffer of the tied stream is flushed whenever the tied stream is read or written. By default, `cin` and `cerr` are both tied to `cout`. Hence, reading cin or writing to cerr flushes the buffer in cout.

`flush` flushes the stream but adds no characters to the output; `ends` inserts a null character into the buffer and then flushes it.

```c++
cout << unitbuf << "Hiya!" << nounitbuf;
```
> Caution: Buffers are not flushed if the program crashes

### Tying Input and Output Streams Together
We can tie either an `istream` or an `ostream` object to another `ostream`.
```c++
cin.tie(&cout);     // cin is tied to cout
ostream *old_tie = cin.tie(nullptr); 
                    // old_tie points to cout, cin is not tied
cin.tie(&cerr);     // cin is tied to cerr
cin.tie(old_tie);   // cin is tied to cout
```
Each stream can be tied to at most one stream at a time. However, mutiple streams can tie themselves to the same `ostream`.

## File Input and Output
```c++
fstream fstrm;
fstream fstrm(s);       // a string (c++ 11) or a c-style character string
fstream fstrm(s, mode);

fstrm.open(s)
fstrm.open(s, mode)
fstrm.close()
fstrm.is_open()
```

Once a file stream has been opened, it remains associated with the specified file. Indeed, calling `open` on a file stream that is already open will fail and set `failbit`.

```c++
for (auto p = argv + 1; p != argv + argc; ++p) {
    ifstream input(*p); // create input and open the file
    if (input){ // if the file is ok, ''process'' this file
        process(input);
    }
    else
        cerr << "couldn't open: " + string(*p);
} // input goes out of scope and is destroyed on each iteration
  // When an fstream object is destroyed, close is called automatically.
```

```c++
/* File Modes */
in
out
app
ate
trunc
binary
```

```c++
ostream out("file1");
ostream out2("file2", ofstream::app);
ostream out3("file3", ofstream::out | ofstream::trunc);
```

## String Streams
```c++
sstream strm;
sstream strm(s);
strm.str()
strm.str(s)
```