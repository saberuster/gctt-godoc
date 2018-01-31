version: 1.9.2

## package token

  `import "go/token"`

## 概述

token包定义了 Go 编程语言中的词汇标记和对这些标记的基本操作（例如：打印，断言）。

## 索引

- [Constants](#pkg-constants)
- [type File](#File)
  - [func (f *File) AddLine(offset int)](#File.AddLine)
  - [func (f *File) AddLineInfo(offset int, filename string, line int)](#File.AddLineInfo)
  - [func (f *File) Base() int](#File.Base)
  - [func (f *File) Line(p Pos) int](#File.Line)
  - [func (f *File) LineCount() int](#File.LineCount)
  - [func (f *File) MergeLine(line int)](#File.MergeLine)
  - [func (f *File) Name() string](#File.Name)
  - [func (f *File) Offset(p Pos) int](#File.Offset)
  - [func (f *File) Pos(offset int) Pos](#File.Pos)
  - [func (f *File) Position(p Pos) (pos Position)](#File.Position)
  - [func (f *File) PositionFor(p Pos, adjusted bool) (pos Position)](#File.PositionFor)
  - [func (f *File) SetLines(lines []int) bool](#File.SetLines)
  - [func (f *File) SetLinesForContent(content []byte)](#File.SetLinesForContent)
  - [func (f *File) Size() int](#File.Size)
- [type FileSet](#FileSet)
  - [func NewFileSet() *FileSet](#NewFileSet)
  - [func (s *FileSet) AddFile(filename string, base, size int) *File](#FileSet.AddFile)
  - [func (s *FileSet) Base() int](#FileSet.Base)
  - [func (s *FileSet) File(p Pos) (f *File)](#FileSet.File)
  - [func (s *FileSet) Iterate(f func(*File) bool)](#FileSet.Iterate)
  - [func (s *FileSet) Position(p Pos) (pos Position)](#FileSet.Position)
  - [func (s *FileSet) PositionFor(p Pos, adjusted bool) (pos Position)](#FileSet.PositionFor)
  - [func (s *FileSet) Read(decode func(interface{}) error) error](#FileSet.Read)
  - [func (s *FileSet) Write(encode func(interface{}) error) error](#FileSet.Write)
- [type Pos](#Pos)
  - [func (p Pos) IsValid() bool](#Pos.IsValid)
- [type Position](#Position)
  - [func (pos *Position) IsValid() bool](#Position.IsValid)
  - [func (pos Position) String() string](#Position.String)
- [type Token](#Token)
  - [func Lookup(ident string) Token](#Lookup)
  - [func (tok Token) IsKeyword() bool](#Token.IsKeyword)
  - [func (tok Token) IsLiteral() bool](#Token.IsLiteral)
  - [func (tok Token) IsOperator() bool](#Token.IsOperator)
  - [func (op Token) Precedence() int](#Token.Precedence)
  - [func (tok Token) String() string](#Token.String)

### 文件

 [position.go](//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go) [serialize.go](//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/serialize.go) [token.go](//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go)

<h2 id="pkg-constants">Constants</h2>

<pre>const (

```
<span id="LowestPrec">LowestPrec</span>  = 0 <span class="comment">// non-operators</span>
<span id="UnaryPrec">UnaryPrec</span>   = 6
<span id="HighestPrec">HighestPrec</span> = 7
```

)</pre>

一个基于优先级表达式解析的常量集合。非运算符拥有最低优先级，它从 1 开始到一元操作符。最高优先级在选择器（selector）、指数（indexing）和其他操作符分隔符标记中作为 "catch-all" 优先级。

<h2 id="File">type <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L81">File</a>

```
<a href="#File">¶</a></h2>
```

<pre>type File struct {

```
<span class="comment">// contains filtered or unexported fields</span>
```

}</pre>

File 是 FileSet 中文件的句柄。File 中包含文件的名称，大小，和行偏移表。

<h3 id="File.AddLine">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L120">AddLine</a>

```
<a href="#File.AddLine">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) AddLine(offset <a href="/builtin/#int">int</a>)</pre>

AddLine 为添加新行的偏移量。行偏移量必须大于上一行的偏移量并且小于文件的大小；否则操作无效。

<h3 id="File.AddLineInfo">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L215">AddLineInfo</a>

```
<a href="#File.AddLineInfo">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) AddLineInfo(offset <a href="/builtin/#int">int</a>, filename <a href="/builtin/#string">string</a>, line <a href="/builtin/#int">int</a>)</pre>

AddLineInfo 在指定文件偏移量添加替代文件和行号信息。偏移量必须大于上一个行信息小于文件大小否则操作无效。

AddLineInfo 的典型应用场景就是在源文件中添加替代位置信息的注释（ //line filename:line）。

<h3 id="File.Base">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L99">Base</a>

```
<a href="#File.Base">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Base() <a href="/builtin/#int">int</a></pre>

Base 获取文件 f 注册时的 base 偏移量。

<h3 id="File.Line">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L248">Line</a>

```
<a href="#File.Line">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Line(p <a href="#Pos">Pos</a>) <a href="/builtin/#int">int</a></pre>

Line 会返回指定位置 p 的行号。p 必须是一个 Pos 类型的值或者为 NoPos。

<h3 id="File.LineCount">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L109">LineCount</a>

```
<a href="#File.LineCount">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) LineCount() <a href="/builtin/#int">int</a></pre>

LineCount 返回文件 f 的行数。

<h3 id="File.MergeLine">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L133">MergeLine</a>

```
<a href="#File.MergeLine">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) MergeLine(line <a href="/builtin/#int">int</a>)</pre>

MergeLine 合并下面 n 行。它的操作与将行末的换行符换成空格的操作相似。可以调用 Position.Line 获取行号。如果 line 无效函数 panic。

<h3 id="File.Name">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L94">Name</a>

```
<a href="#File.Name">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Name() <a href="/builtin/#string">string</a></pre>

Name 返回 AddFile 中注册的文件名称。

<h3 id="File.Offset">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L238">Offset</a>

```
<a href="#File.Offset">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Offset(p <a href="#Pos">Pos</a>) <a href="/builtin/#int">int</a></pre>

Offset 返回给定位置 p 的偏移量。p 必须是一个有效的 Pos 类型值。在一个文件中 f.Offset(f.Pos(offset)) == offset。

<h3 id="File.Pos">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L227">Pos</a>

```
<a href="#File.Pos">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Pos(offset <a href="/builtin/#int">int</a>) <a href="#Pos">Pos</a></pre>

Pos 返回指定偏移量的 Pos 值。这个偏移值必须小于 f.Size()。 f.Pos(f.Offset(p)) = p。

<h3 id="File.Position">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L305">Position</a>

```
<a href="#File.Position">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Position(p <a href="#Pos">Pos</a>) (pos <a href="#Position">Position</a>)</pre>

Position 返回给定文件位置 p 的 Position 类型值。调用 f.Position(p) 和 f.PositionFor(p,true) 是相同的。

<h3 id="File.PositionFor">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L292">PositionFor</a>

```
<a href="#File.PositionFor">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) PositionFor(p <a href="#Pos">Pos</a>, adjusted <a href="/builtin/#bool">bool</a>) (pos <a href="#Position">Position</a>)</pre>

Position 返回给定文件位置 p 的 Position 类型值。如果设置 adjusted 会计算注释对偏移量的影响，否则注释会被忽略。p 必须是一个 Pos 类型值或者 NoPos。

<h3 id="File.SetLines">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L160">SetLines</a>

```
<a href="#File.SetLines">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) SetLines(lines []<a href="/builtin/#int">int</a>) <a href="/builtin/#bool">bool</a></pre>

SetLines sets the line offsets for a file and reports whether it succeeded. The
line offsets are the offsets of the first character of each line; for instance
for the content "ab\nc\n" the line offsets are {0, 3}. An empty file has an
empty line offset table. Each line offset must be larger than the offset for the
previous line and smaller than the file size; otherwise SetLines fails and
returns false. Callers must not mutate the provided slice after SetLines
returns.

SetLines 为文件设置行偏移量并且返回是否操作成功。行偏移量是基于每行第一个字符的偏移量。例如在内容 "ab\nc\n" 中，行偏移量就是 {0,3}。一个空文件有一个空的偏移量表。每个偏移量必须大于前一行的偏移量并且小于文件大小。否则 SetLines 将会失败并返回 fails。用户在 SetLines 返回后不能修改给定的切片。

<h3 id="File.SetLinesForContent">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L178">SetLinesForContent</a>

```
<a href="#File.SetLinesForContent">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) SetLinesForContent(content []<a href="/builtin/#byte">byte</a>)</pre>

SetLinesForContent sets the line offsets for the given file content. It ignores
position-altering //line comments.

SetLinesForContent 为指定的文件内容设置行偏移。他会忽略注释行。

<h3 id="File.Size">func (*File) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L104">Size</a>

```
<a href="#File.Size">¶</a></h3>
```

<pre>func (f *<a href="#File">File</a>) Size() <a href="/builtin/#int">int</a></pre>

Size returns the size of file f as registered with AddFile.

Size 返回AddFile 注册的文件 f 的大小。

<h2 id="FileSet">type <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L316">FileSet</a>

```
<a href="#FileSet">¶</a></h2>
```

<pre>type FileSet struct {

```
<span class="comment">// contains filtered or unexported fields</span>
```

}</pre>

FileSet 相当于源文件集合。它的方法都是同步的可以在多个 goroutine 中并发调用。

<h3 id="NewFileSet">func <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L324">NewFileSet</a>

```
<a href="#NewFileSet">¶</a></h3>
```

<pre>func NewFileSet() *<a href="#FileSet">FileSet</a></pre>

NewFileSet 创建一个新的源文件集合。

<h3 id="FileSet.AddFile">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L357">AddFile</a>

```
<a href="#FileSet.AddFile">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) AddFile(filename <a href="/builtin/#string">string</a>, base, size <a href="/builtin/#int">int</a>) *<a href="#File">File</a></pre>

AddFile 根据给定的文件名，基础偏移量和文件大小来添加并返回一个新的文件。多个文件可以使用相同的名称。基础偏移量不能小于 FileSet 的 Base() 返回值并且 size 不能为负数。如果基础偏移量为负会使用 FileSet 的 Base() 返回值。

添加文件会把下次添加文件时的 base 值变为 base + size + 1。 Pos 值 p 和给定文件偏移量 offs 的关系：

```
int(p) = base + offs
```

 offs 在 [0,size] 范围内，所以 p 在 [base,base+size] 范围内。为了方便起见，File.Pos 可以根据文件偏移量获取对应位置值。

<h3 id="FileSet.Base">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L333">Base</a>

```
<a href="#FileSet.Base">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) Base() <a href="/builtin/#int">int</a></pre>

Base 返回 AddFile 所需的最小的偏移量。

<h3 id="FileSet.File">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L427">File</a>

```
<a href="#FileSet.File">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) File(p <a href="#Pos">Pos</a>) (f *<a href="#File">File</a>)</pre>

File 返回位置 p 所在文件，如果没有这个文件或者 p==NoPos返回 nil。

<h3 id="FileSet.Iterate">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L382">Iterate</a>

```
<a href="#FileSet.Iterate">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) Iterate(f func(*<a href="#File">File</a>) <a href="/builtin/#bool">bool</a>)</pre>

Iterate 按照文件添加顺序调用 f 直到 f 返回 false。

<h3 id="FileSet.Position">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L451">Position</a>

```
<a href="#FileSet.Position">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) Position(p <a href="#Pos">Pos</a>) (pos <a href="#Position">Position</a>)</pre>

Position 将 Pos 类型的 p 转换为 fileset 中的 Position 值。调用 s.Position(p) 和调用 s.PositionFor(p,true) 是相等的。

<h3 id="FileSet.PositionFor">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L439">PositionFor</a>

```
<a href="#FileSet.PositionFor">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) PositionFor(p <a href="#Pos">Pos</a>, adjusted <a href="/builtin/#bool">bool</a>) (pos <a href="#Position">Position</a>)</pre>

PositionFor 将 Pos 类型值转换成 Position 类型值。如果设置了 adjusted 该位置会计算注释行的影响，否则注释将会被忽略。p 必须是一个 Pos 类型值或者 NoPos。

<h3 id="FileSet.Read">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/serialize.go#L12">Read</a>

```
<a href="#FileSet.Read">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) Read(decode func(interface{}) <a href="/builtin/#error">error</a>) <a href="/builtin/#error">error</a></pre>

Read 将文件集合反序列化进 s ，s 不能为 nil。

<h3 id="FileSet.Write">func (*FileSet) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/serialize.go#L40">Write</a>

```
<a href="#FileSet.Write">¶</a></h3>
```

<pre>func (s *<a href="#FileSet">FileSet</a>) Write(encode func(interface{}) <a href="/builtin/#error">error</a>) <a href="/builtin/#error">error</a></pre>

Write 序列化文件 s。

<h2 id="Pos">type <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L61">Pos</a>

```
<a href="#Pos">¶</a></h2>
```

<pre>type Pos <a href="/builtin/#int">int</a></pre>

Pos is a compact encoding of a source position within a file set. It can be
converted into a Position for a more convenient, but much larger,
representation.

Pos 是一个聚合文件集合中文件位置的编码。他能很容易的转换成更大更方便的 Position 类型。

The Pos value for a given file is a number in the range [base, base+size], where
base and size are specified when adding the file to the file set via AddFile.

一个指定文件的 Pos 值是一个在 [base,base+size] 的值。base 和 size 通过 AddFile 指定。

To create the Pos value for a specific source offset (measured in bytes), first
add the respective file to the current file set using FileSet.AddFile and then
call File.Pos(offset) for that file. Given a Pos value p for a specific file set
fset, the corresponding Position value is obtained by calling fset.Position(p).

为了创建一个指定资源的偏移量，首先将文件使用 FileSet.AddFile 添加进来，然后调用指定File 的 File.Pos(offset)。

给定一个 Pos 类型值，对应的 Position 值可以通过 fset.Position(p)。来获得。

Pos values can be compared directly with the usual comparison operators: If two
Pos values p and q are in the same file, comparing p and q is equivalent to
comparing the respective source file offsets. If p and q are in different files,
p < q is true if the file implied by p was added to the respective file set
before the file implied by q.

Pos 值可以用比较运算符直接比较。如果两个 Pos 值 p 和 q 在相同的文件中。如果他们代表相同的偏移量，p == q。

如果 p 和 q 在不同的文件中，如果 p 所在的文件在 q 之前添加，那么 p < q。

<pre>const <span id="NoPos">NoPos</span> <a href="#Pos">Pos</a> = 0</pre>

The zero value for Pos is NoPos; there is no file and line information
associated with it, and NoPos.IsValid() is false. NoPos is always smaller than
any other Pos value. The corresponding Position value for NoPos is the zero
value for Position.

Pos 的零值是 NoPos。没有文件和行信息。并且 NoPos.IsValid() 返回 false。NoPos 总是小于其他 Pos 值。NoPos 对应的Position类型值是 Position 类型的零值。

<h3 id="Pos.IsValid">func (Pos) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L71">IsValid</a>

```
<a href="#Pos.IsValid">¶</a></h3>

```

<pre>func (p <a href="#Pos">Pos</a>) IsValid() <a href="/builtin/#bool">bool</a></pre>

IsValid reports whether the position is valid.

IsValid 判断 position 是否有效。

<h2 id="Position">type <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L10">Position</a>

```
<a href="#Position">¶</a></h2>

```

<pre>type Position struct {
<span id="Position.Filename"></span>    Filename <a href="/builtin/#string">string</a> <span class="comment">// filename, if any</span>
<span id="Position.Offset"></span>    Offset   <a href="/builtin/#int">int</a>    <span class="comment">// offset, starting at 0</span>
<span id="Position.Line"></span>    Line     <a href="/builtin/#int">int</a>    <span class="comment">// line number, starting at 1</span>
<span id="Position.Column"></span>    Column   <a href="/builtin/#int">int</a>    <span class="comment">// column number, starting at 1 (byte count)</span>
}</pre>

Position describes an arbitrary source position including the file, line, and
column location. A Position is valid if the line number is > 0.

Position 表示一个资源的绝对位置，包括文件，行数，列数。如果 Position 是合法的那么行号一定大于零。

<h3 id="Position.IsValid">func (*Position) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L18">IsValid</a>

```
<a href="#Position.IsValid">¶</a></h3>

```

<pre>func (pos *<a href="#Position">Position</a>) IsValid() <a href="/builtin/#bool">bool</a></pre>

IsValid reports whether the position is valid.

IsValid 判断 Position 是否有效。

<h3 id="Position.String">func (Position) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/position.go#L27">String</a>

```
<a href="#Position.String">¶</a></h3>

```

<pre>func (pos <a href="#Position">Position</a>) String() <a href="/builtin/#string">string</a></pre>

String returns a string in one of several forms:

String 返回下面几种格式中的一种：

```
file:line:column    valid position with file name
line:column         valid position without file name
file                invalid position with file name
-                   invalid position without file name

```

<h2 id="Token">type <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L3">Token</a>

```
<a href="#Token">¶</a></h2>

```

<pre>type Token <a href="/builtin/#int">int</a></pre>

Token is the set of lexical tokens of the Go programming language.

Token 是 Go 的词汇标记集合。

<pre>const (

```
<span class="comment">// Special tokens</span>
<span id="ILLEGAL">ILLEGAL</span> <a href="#Token">Token</a> = <a href="/builtin/#iota">iota</a>
<span id="EOF">EOF</span>
<span id="COMMENT">COMMENT</span>

<span class="comment">// Identifiers and basic type literals</span>
<span class="comment">// (these tokens stand for classes of literals)</span>
<span id="IDENT">IDENT</span>  <span class="comment">// main</span>
<span id="INT">INT</span>    <span class="comment">// 12345</span>
<span id="FLOAT">FLOAT</span>  <span class="comment">// 123.45</span>
<span id="IMAG">IMAG</span>   <span class="comment">// 123.45i</span>
<span id="CHAR">CHAR</span>   <span class="comment">// &#39;a&#39;</span>
<span id="STRING">STRING</span> <span class="comment">// &#34;abc&#34;</span>

<span class="comment">// Operators and delimiters</span>
<span id="ADD">ADD</span> <span class="comment">// +</span>
<span id="SUB">SUB</span> <span class="comment">// -</span>
<span id="MUL">MUL</span> <span class="comment">// *</span>
<span id="QUO">QUO</span> <span class="comment">// /</span>
<span id="REM">REM</span> <span class="comment">// %</span>

<span id="AND">AND</span>     <span class="comment">// &amp;</span>
<span id="OR">OR</span>      <span class="comment">// |</span>
<span id="XOR">XOR</span>     <span class="comment">// ^</span>
<span id="SHL">SHL</span>     <span class="comment">// &lt;&lt;</span>
<span id="SHR">SHR</span>     <span class="comment">// &gt;&gt;</span>
<span id="AND_NOT">AND_NOT</span> <span class="comment">// &amp;^</span>

<span id="ADD_ASSIGN">ADD_ASSIGN</span> <span class="comment">// +=</span>
<span id="SUB_ASSIGN">SUB_ASSIGN</span> <span class="comment">// -=</span>
<span id="MUL_ASSIGN">MUL_ASSIGN</span> <span class="comment">// *=</span>
<span id="QUO_ASSIGN">QUO_ASSIGN</span> <span class="comment">// /=</span>
<span id="REM_ASSIGN">REM_ASSIGN</span> <span class="comment">// %=</span>

<span id="AND_ASSIGN">AND_ASSIGN</span>     <span class="comment">// &amp;=</span>
<span id="OR_ASSIGN">OR_ASSIGN</span>      <span class="comment">// |=</span>
<span id="XOR_ASSIGN">XOR_ASSIGN</span>     <span class="comment">// ^=</span>
<span id="SHL_ASSIGN">SHL_ASSIGN</span>     <span class="comment">// &lt;&lt;=</span>
<span id="SHR_ASSIGN">SHR_ASSIGN</span>     <span class="comment">// &gt;&gt;=</span>
<span id="AND_NOT_ASSIGN">AND_NOT_ASSIGN</span> <span class="comment">// &amp;^=</span>

<span id="LAND">LAND</span>  <span class="comment">// &amp;&amp;</span>
<span id="LOR">LOR</span>   <span class="comment">// ||</span>
<span id="ARROW">ARROW</span> <span class="comment">// &lt;-</span>
<span id="INC">INC</span>   <span class="comment">// ++</span>
<span id="DEC">DEC</span>   <span class="comment">// --</span>

<span id="EQL">EQL</span>    <span class="comment">// ==</span>
<span id="LSS">LSS</span>    <span class="comment">// &lt;</span>
<span id="GTR">GTR</span>    <span class="comment">// &gt;</span>
<span id="ASSIGN">ASSIGN</span> <span class="comment">// =</span>
<span id="NOT">NOT</span>    <span class="comment">// !</span>

<span id="NEQ">NEQ</span>      <span class="comment">// !=</span>
<span id="LEQ">LEQ</span>      <span class="comment">// &lt;=</span>
<span id="GEQ">GEQ</span>      <span class="comment">// &gt;=</span>
<span id="DEFINE">DEFINE</span>   <span class="comment">// :=</span>
<span id="ELLIPSIS">ELLIPSIS</span> <span class="comment">// ...</span>

<span id="LPAREN">LPAREN</span> <span class="comment">// (</span>
<span id="LBRACK">LBRACK</span> <span class="comment">// [</span>
<span id="LBRACE">LBRACE</span> <span class="comment">// {</span>
<span id="COMMA">COMMA</span>  <span class="comment">// ,</span>
<span id="PERIOD">PERIOD</span> <span class="comment">// .</span>

<span id="RPAREN">RPAREN</span>    <span class="comment">// )</span>
<span id="RBRACK">RBRACK</span>    <span class="comment">// ]</span>
<span id="RBRACE">RBRACE</span>    <span class="comment">// }</span>
<span id="SEMICOLON">SEMICOLON</span> <span class="comment">// ;</span>
<span id="COLON">COLON</span>     <span class="comment">// :</span>

<span class="comment">// Keywords</span>
<span id="BREAK">BREAK</span>
<span id="CASE">CASE</span>
<span id="CHAN">CHAN</span>
<span id="CONST">CONST</span>
<span id="CONTINUE">CONTINUE</span>

<span id="DEFAULT">DEFAULT</span>
<span id="DEFER">DEFER</span>
<span id="ELSE">ELSE</span>
<span id="FALLTHROUGH">FALLTHROUGH</span>
<span id="FOR">FOR</span>

<span id="FUNC">FUNC</span>
<span id="GO">GO</span>
<span id="GOTO">GOTO</span>
<span id="IF">IF</span>
<span id="IMPORT">IMPORT</span>

<span id="INTERFACE">INTERFACE</span>
<span id="MAP">MAP</span>
<span id="PACKAGE">PACKAGE</span>
<span id="RANGE">RANGE</span>
<span id="RETURN">RETURN</span>

<span id="SELECT">SELECT</span>
<span id="STRUCT">STRUCT</span>
<span id="SWITCH">SWITCH</span>
<span id="TYPE">TYPE</span>
<span id="VAR">VAR</span>

```

)</pre>

The list of tokens.

标记列表。

<h3 id="Lookup">func <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L276">Lookup</a>

```
<a href="#Lookup">¶</a></h3>

```

<pre>func Lookup(ident <a href="/builtin/#string">string</a>) <a href="#Token">Token</a></pre>

Lookup maps an identifier to its keyword token or IDENT (if not a keyword).

Lookup 会找到 ident 对应的关键字标记或者 IDENT（如果不是一个关键字）。

<h3 id="Token.IsKeyword">func (Token) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L298">IsKeyword</a>

```
<a href="#Token.IsKeyword">¶</a></h3>

```

<pre>func (tok <a href="#Token">Token</a>) IsKeyword() <a href="/builtin/#bool">bool</a></pre>

IsKeyword returns true for tokens corresponding to keywords; it returns false
otherwise.

IsKeyword 在 token 对应关键字的时候返回 true 否则返回 false。

<h3 id="Token.IsLiteral">func (Token) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L288">IsLiteral</a>

```
<a href="#Token.IsLiteral">¶</a></h3>

```

<pre>func (tok <a href="#Token">Token</a>) IsLiteral() <a href="/builtin/#bool">bool</a></pre>

IsLiteral returns true for tokens corresponding to identifiers and basic type
literals; it returns false otherwise.

IsLiteral 在 token 对应标识符或者基础类型字面值的时候返回 true 否则返回 false。

<h3 id="Token.IsOperator">func (Token) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L293">IsOperator</a>

```
<a href="#Token.IsOperator">¶</a></h3>

```

<pre>func (tok <a href="#Token">Token</a>) IsOperator() <a href="/builtin/#bool">bool</a></pre>

IsOperator returns true for tokens corresponding to operators and delimiters; it
returns false otherwise.

IsOperator 在 token 对应 操作符或者分隔符的时候返回 true 否则返回 false。

<h3 id="Token.Precedence">func (Token) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L249">Precedence</a>

```
<a href="#Token.Precedence">¶</a></h3>

```

<pre>func (op <a href="#Token">Token</a>) Precedence() <a href="/builtin/#int">int</a></pre>

Precedence returns the operator precedence of the binary operator op. If op is
not a binary operator, the result is LowestPrecedence.

Precedence 返回二进制操作符 op 的优先级。如果 op 不是一个二进制的操作符返回 LowestPrecedence。

<h3 id="Token.String">func (Token) <a href="//github.com/golang/go/blob/2ea7d3461bb41d0ae12b56ee52d43314bcdb97f9/src/go/token/token.go#L222">String</a>

```
<a href="#Token.String">¶</a></h3>

```

<pre>func (tok <a href="#Token">Token</a>) String() <a href="/builtin/#string">string</a></pre>

String returns the string corresponding to the token tok. For operators,
delimiters, and keywords the string is the actual token character sequence
(e.g., for the token ADD, the string is "+"). For all other tokens the string
corresponds to the token constant name (e.g. for the token IDENT, the string is
"IDENT").

String 返回 token 对应的字符串。对于操作符，分隔符，关键字，字符串实际上是字符序列。其他的 token 对应的字符串是他们的常量名称。

