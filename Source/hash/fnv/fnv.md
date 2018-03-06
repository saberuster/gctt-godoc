version: 1.10
## package fnv

  `import "hash/fnv"`

## Overview

Package fnv implements FNV-1 and FNV-1a, non-cryptographic hash functions
created by Glenn Fowler, Landon Curt Noll, and Phong Vo. See
https://en.wikipedia.org/wiki/Fowler-Noll-Vo_hash_function.

All the hash.Hash implementations returned by this package also implement
encoding.BinaryMarshaler and encoding.BinaryUnmarshaler to marshal and unmarshal
the internal state of the hash.

## Index

- [func New128() hash.Hash](#New128)
- [func New128a() hash.Hash](#New128a)
- [func New32() hash.Hash32](#New32)
- [func New32a() hash.Hash32](#New32a)
- [func New64() hash.Hash64](#New64)
- [func New64a() hash.Hash64](#New64a)

### Package files
 [fnv.go](//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go)

<h2 id="New128">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go#L60">New128</a>
    <a href="#New128">¶</a></h2>
<pre>func New128() <a href="/hash/">hash</a>.<a href="/hash/#Hash">Hash</a></pre>

New128 returns a new 128-bit FNV-1 hash.Hash. Its Sum method will lay the value
out in big-endian byte order.

<h2 id="New128a">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go#L69">New128a</a>
    <a href="#New128a">¶</a></h2>
<pre>func New128a() <a href="/hash/">hash</a>.<a href="/hash/#Hash">Hash</a></pre>

New128a returns a new 128-bit FNV-1a hash.Hash. Its Sum method will lay the
value out in big-endian byte order.

<h2 id="New32">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go#L32">New32</a>
    <a href="#New32">¶</a></h2>
<pre>func New32() <a href="/hash/">hash</a>.<a href="/hash/#Hash32">Hash32</a></pre>

New32 returns a new 32-bit FNV-1 hash.Hash. Its Sum method will lay the value
out in big-endian byte order.

<h2 id="New32a">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go#L39">New32a</a>
    <a href="#New32a">¶</a></h2>
<pre>func New32a() <a href="/hash/">hash</a>.<a href="/hash/#Hash32">Hash32</a></pre>

New32a returns a new 32-bit FNV-1a hash.Hash. Its Sum method will lay the value
out in big-endian byte order.

<h2 id="New64">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go#L46">New64</a>
    <a href="#New64">¶</a></h2>
<pre>func New64() <a href="/hash/">hash</a>.<a href="/hash/#Hash64">Hash64</a></pre>

New64 returns a new 64-bit FNV-1 hash.Hash. Its Sum method will lay the value
out in big-endian byte order.

<h2 id="New64a">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/hash/fnv/fnv.go#L53">New64a</a>
    <a href="#New64a">¶</a></h2>
<pre>func New64a() <a href="/hash/">hash</a>.<a href="/hash/#Hash64">Hash64</a></pre>

New64a returns a new 64-bit FNV-1a hash.Hash. Its Sum method will lay the value
out in big-endian byte order.


