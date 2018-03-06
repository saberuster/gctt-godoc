version: 1.10
## package zip

  `import "archive/zip"`

## Overview

Package zip provides support for reading and writing ZIP archives.

See: https://www.pkware.com/appnote

This package does not support disk spanning.

A note about ZIP64:

To be backwards compatible the FileHeader has both 32 and 64 bit Size fields.
The 64 bit fields will always contain the correct value and for normal archives
both fields will be the same. For files requiring the ZIP64 format the 32 bit
fields will be 0xffffffff and the 64 bit fields must be used instead.

## Index

- [Constants](#pkg-constants)
- [Variables](#pkg-variables)
- [func RegisterCompressor(method uint16, comp Compressor)](#RegisterCompressor)
- [func RegisterDecompressor(method uint16, dcomp Decompressor)](#RegisterDecompressor)
- [type Compressor](#Compressor)
- [type Decompressor](#Decompressor)
- [type File](#File)
  - [func (f *File) DataOffset() (offset int64, err error)](#File.DataOffset)
  - [func (f *File) Open() (io.ReadCloser, error)](#File.Open)
- [type FileHeader](#FileHeader)
  - [func FileInfoHeader(fi os.FileInfo) (*FileHeader, error)](#FileInfoHeader)
  - [func (h *FileHeader) FileInfo() os.FileInfo](#FileHeader.FileInfo)
  - [func (h *FileHeader) ModTime() time.Time](#FileHeader.ModTime)
  - [func (h *FileHeader) Mode() (mode os.FileMode)](#FileHeader.Mode)
  - [func (h *FileHeader) SetModTime(t time.Time)](#FileHeader.SetModTime)
  - [func (h *FileHeader) SetMode(mode os.FileMode)](#FileHeader.SetMode)
- [type ReadCloser](#ReadCloser)
  - [func OpenReader(name string) (*ReadCloser, error)](#OpenReader)
  - [func (rc *ReadCloser) Close() error](#ReadCloser.Close)
- [type Reader](#Reader)
  - [func NewReader(r io.ReaderAt, size int64) (*Reader, error)](#NewReader)
  - [func (z *Reader) RegisterDecompressor(method uint16, dcomp Decompressor)](#Reader.RegisterDecompressor)
- [type Writer](#Writer)
  - [func NewWriter(w io.Writer) *Writer](#NewWriter)
  - [func (w *Writer) Close() error](#Writer.Close)
  - [func (w *Writer) Create(name string) (io.Writer, error)](#Writer.Create)
  - [func (w *Writer) CreateHeader(fh *FileHeader) (io.Writer, error)](#Writer.CreateHeader)
  - [func (w *Writer) Flush() error](#Writer.Flush)
  - [func (w *Writer) RegisterCompressor(method uint16, comp Compressor)](#Writer.RegisterCompressor)
  - [func (w *Writer) SetComment(comment string) error](#Writer.SetComment)
  - [func (w *Writer) SetOffset(n int64)](#Writer.SetOffset)

### Examples

- [Reader](#exampleReader)
- [Writer](#exampleWriter)
- [Writer.RegisterCompressor](#exampleWriter_RegisterCompressor)

### Package files
 [reader.go](//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go) [register.go](//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/register.go) [struct.go](//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go) [writer.go](//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go)

<h2 id="pkg-constants">Constants</h2>

<pre>const (
    <span id="Store">Store</span>   <a href="/builtin/#uint16">uint16</a> = 0 <span class="comment">// no compression</span>
    <span id="Deflate">Deflate</span> <a href="/builtin/#uint16">uint16</a> = 8 <span class="comment">// DEFLATE compressed</span>
)</pre>

Compression methods.

<h2 id="pkg-variables">Variables</h2>

<pre>var (
    <span id="ErrFormat">ErrFormat</span>    = <a href="/errors/">errors</a>.<a href="/errors/#New">New</a>(&#34;zip: not a valid zip file&#34;)
    <span id="ErrAlgorithm">ErrAlgorithm</span> = <a href="/errors/">errors</a>.<a href="/errors/#New">New</a>(&#34;zip: unsupported compression algorithm&#34;)
    <span id="ErrChecksum">ErrChecksum</span>  = <a href="/errors/">errors</a>.<a href="/errors/#New">New</a>(&#34;zip: checksum error&#34;)
)</pre>


<h2 id="RegisterCompressor">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/register.go#L118">RegisterCompressor</a>
    <a href="#RegisterCompressor">¶</a></h2>
<pre>func RegisterCompressor(method <a href="/builtin/#uint16">uint16</a>, comp <a href="#Compressor">Compressor</a>)</pre>

RegisterCompressor registers custom compressors for a specified method ID. The
common methods Store and Deflate are built in.

<h2 id="RegisterDecompressor">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/register.go#L110">RegisterDecompressor</a>
    <a href="#RegisterDecompressor">¶</a></h2>
<pre>func RegisterDecompressor(method <a href="/builtin/#uint16">uint16</a>, dcomp <a href="#Decompressor">Decompressor</a>)</pre>

RegisterDecompressor allows custom decompressors for a specified method ID. The
common methods Store and Deflate are built in.

<h2 id="Compressor">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/register.go#L10">Compressor</a>
    <a href="#Compressor">¶</a></h2>
<pre>type Compressor func(w <a href="/io/">io</a>.<a href="/io/#Writer">Writer</a>) (<a href="/io/">io</a>.<a href="/io/#WriteCloser">WriteCloser</a>, <a href="/builtin/#error">error</a>)</pre>

A Compressor returns a new compressing writer, writing to w. The WriteCloser's
Close method must be used to flush pending data to w. The Compressor itself must
be safe to invoke from multiple goroutines simultaneously, but each returned
writer will be used only by one goroutine at a time.

<h2 id="Decompressor">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/register.go#L17">Decompressor</a>
    <a href="#Decompressor">¶</a></h2>
<pre>type Decompressor func(r <a href="/io/">io</a>.<a href="/io/#Reader">Reader</a>) <a href="/io/">io</a>.<a href="/io/#ReadCloser">ReadCloser</a></pre>

A Decompressor returns a new decompressing reader, reading from r. The
ReadCloser's Close method must be used to release associated resources. The
Decompressor itself must be safe to invoke from multiple goroutines
simultaneously, but each returned reader will be used only by one goroutine at a
time.

<h2 id="File">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L27">File</a>
    <a href="#File">¶</a></h2>
<pre>type File struct {
    <a href="#FileHeader">FileHeader</a>
    <span class="comment">// contains filtered or unexported fields</span>
}</pre>


<h3 id="File.DataOffset">func (*File) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L137">DataOffset</a>
    <a href="#File.DataOffset">¶</a></h3>
<pre>func (f *<a href="#File">File</a>) DataOffset() (offset <a href="/builtin/#int64">int64</a>, err <a href="/builtin/#error">error</a>)</pre>

DataOffset returns the offset of the file's possibly-compressed data, relative
to the beginning of the zip file.

Most callers should instead use Open, which transparently decompresses data and
verifies checksums.

<h3 id="File.Open">func (*File) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L147">Open</a>
    <a href="#File.Open">¶</a></h3>
<pre>func (f *<a href="#File">File</a>) Open() (<a href="/io/">io</a>.<a href="/io/#ReadCloser">ReadCloser</a>, <a href="/builtin/#error">error</a>)</pre>

Open returns a ReadCloser that provides access to the File's contents. Multiple
files may be read concurrently.

<h2 id="FileHeader">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L72">FileHeader</a>
    <a href="#FileHeader">¶</a></h2>
<pre>type FileHeader struct {
<span id="FileHeader.Name"></span>    <span class="comment">// Name is the name of the file.</span>
    <span class="comment">// It must be a relative path, not start with a drive letter (e.g. C:),</span>
    <span class="comment">// and must use forward slashes instead of back slashes.</span>
    Name <a href="/builtin/#string">string</a>

<span id="FileHeader.Comment"></span>    <span class="comment">// Comment is any arbitrary user-defined string shorter than 64KiB.</span>
    Comment <a href="/builtin/#string">string</a>

<span id="FileHeader.NonUTF8"></span>    <span class="comment">// NonUTF8 indicates that Name and Comment are not encoded in UTF-8.</span>
    <span class="comment">//</span>
    <span class="comment">// By specification, the only other encoding permitted should be CP-437,</span>
    <span class="comment">// but historically many ZIP readers interpret Name and Comment as whatever</span>
    <span class="comment">// the system&#39;s local character encoding happens to be.</span>
    <span class="comment">//</span>
    <span class="comment">// This flag should only be set if the user intends to encode a non-portable</span>
    <span class="comment">// ZIP file for a specific localized region. Otherwise, the Writer</span>
    <span class="comment">// automatically sets the ZIP format&#39;s UTF-8 flag for valid UTF-8 strings.</span>
    NonUTF8 <a href="/builtin/#bool">bool</a>

<span id="FileHeader.CreatorVersion"></span>    CreatorVersion <a href="/builtin/#uint16">uint16</a>
<span id="FileHeader.ReaderVersion"></span>    ReaderVersion  <a href="/builtin/#uint16">uint16</a>
<span id="FileHeader.Flags"></span>    Flags          <a href="/builtin/#uint16">uint16</a>

<span id="FileHeader.Method"></span>    <span class="comment">// Method is the compression method. If zero, Store is used.</span>
    Method <a href="/builtin/#uint16">uint16</a>

<span id="FileHeader.Modified"></span>    <span class="comment">// Modified is the modified time of the file.</span>
    <span class="comment">//</span>
    <span class="comment">// When reading, an extended timestamp is preferred over the legacy MS-DOS</span>
    <span class="comment">// date field, and the offset between the times is used as the timezone.</span>
    <span class="comment">// If only the MS-DOS date is present, the timezone is assumed to be UTC.</span>
    <span class="comment">//</span>
    <span class="comment">// When writing, an extended timestamp (which is timezone-agnostic) is</span>
    <span class="comment">// always emitted. The legacy MS-DOS date field is encoded according to the</span>
    <span class="comment">// location of the Modified time.</span>
    Modified     <a href="/time/">time</a>.<a href="/time/#Time">Time</a>
<span id="FileHeader.ModifiedTime"></span>    ModifiedTime <a href="/builtin/#uint16">uint16</a> <span class="comment">// Deprecated: Legacy MS-DOS date; use Modified instead.</span>
<span id="FileHeader.ModifiedDate"></span>    ModifiedDate <a href="/builtin/#uint16">uint16</a> <span class="comment">// Deprecated: Legacy MS-DOS time; use Modified instead.</span>

<span id="FileHeader.CRC32"></span>    CRC32              <a href="/builtin/#uint32">uint32</a>
<span id="FileHeader.CompressedSize"></span>    CompressedSize     <a href="/builtin/#uint32">uint32</a> <span class="comment">// Deprecated: Use CompressedSize64 instead.</span>
<span id="FileHeader.UncompressedSize"></span>    UncompressedSize   <a href="/builtin/#uint32">uint32</a> <span class="comment">// Deprecated: Use UncompressedSize64 instead.</span>
<span id="FileHeader.CompressedSize64"></span>    CompressedSize64   <a href="/builtin/#uint64">uint64</a>
<span id="FileHeader.UncompressedSize64"></span>    UncompressedSize64 <a href="/builtin/#uint64">uint64</a>
<span id="FileHeader.Extra"></span>    Extra              []<a href="/builtin/#byte">byte</a>
<span id="FileHeader.ExternalAttrs"></span>    ExternalAttrs      <a href="/builtin/#uint32">uint32</a> <span class="comment">// Meaning depends on CreatorVersion</span>
}</pre>

FileHeader describes a file within a zip file. See the zip spec for details.

<h3 id="FileInfoHeader">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L150">FileInfoHeader</a>
    <a href="#FileInfoHeader">¶</a></h3>
<pre>func FileInfoHeader(fi <a href="/os/">os</a>.<a href="/os/#FileInfo">FileInfo</a>) (*<a href="#FileHeader">FileHeader</a>, <a href="/builtin/#error">error</a>)</pre>

FileInfoHeader creates a partially-populated FileHeader from an os.FileInfo.
Because os.FileInfo's Name method returns only the base name of the file it
describes, it may be necessary to modify the Name field of the returned header
to provide the full path name of the file. If compression is desired, callers
should set the FileHeader.Method field; it is unset by default.

<h3 id="FileHeader.FileInfo">func (*FileHeader) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L122">FileInfo</a>
    <a href="#FileHeader.FileInfo">¶</a></h3>
<pre>func (h *<a href="#FileHeader">FileHeader</a>) FileInfo() <a href="/os/">os</a>.<a href="/os/#FileInfo">FileInfo</a></pre>

FileInfo returns an os.FileInfo for the FileHeader.

<h3 id="FileHeader.ModTime">func (*FileHeader) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L225">ModTime</a>
    <a href="#FileHeader.ModTime">¶</a></h3>
<pre>func (h *<a href="#FileHeader">FileHeader</a>) ModTime() <a href="/time/">time</a>.<a href="/time/#Time">Time</a></pre>

ModTime returns the modification time in UTC using the legacy ModifiedDate and
ModifiedTime fields.

Deprecated: Use Modified instead.

<h3 id="FileHeader.Mode">func (*FileHeader) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L259">Mode</a>
    <a href="#FileHeader.Mode">¶</a></h3>
<pre>func (h *<a href="#FileHeader">FileHeader</a>) Mode() (mode <a href="/os/">os</a>.<a href="/os/#FileMode">FileMode</a>)</pre>

Mode returns the permission and mode bits for the FileHeader.

<h3 id="FileHeader.SetModTime">func (*FileHeader) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L233">SetModTime</a>
    <a href="#FileHeader.SetModTime">¶</a></h3>
<pre>func (h *<a href="#FileHeader">FileHeader</a>) SetModTime(t <a href="/time/">time</a>.<a href="/time/#Time">Time</a>)</pre>

SetModTime sets the Modified, ModifiedTime, and ModifiedDate fields to the given
time in UTC.

Deprecated: Use Modified instead.

<h3 id="FileHeader.SetMode">func (*FileHeader) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/struct.go#L273">SetMode</a>
    <a href="#FileHeader.SetMode">¶</a></h3>
<pre>func (h *<a href="#FileHeader">FileHeader</a>) SetMode(mode <a href="/os/">os</a>.<a href="/os/#FileMode">FileMode</a>)</pre>

SetMode changes the permission and mode bits for the FileHeader.

<h2 id="ReadCloser">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L22">ReadCloser</a>
    <a href="#ReadCloser">¶</a></h2>
<pre>type ReadCloser struct {
    <a href="#Reader">Reader</a>
    <span class="comment">// contains filtered or unexported fields</span>
}</pre>


<h3 id="OpenReader">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L40">OpenReader</a>
    <a href="#OpenReader">¶</a></h3>
<pre>func OpenReader(name <a href="/builtin/#string">string</a>) (*<a href="#ReadCloser">ReadCloser</a>, <a href="/builtin/#error">error</a>)</pre>

OpenReader will open the Zip file specified by name and return a ReadCloser.

<h3 id="ReadCloser.Close">func (*ReadCloser) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L128">Close</a>
    <a href="#ReadCloser.Close">¶</a></h3>
<pre>func (rc *<a href="#ReadCloser">ReadCloser</a>) Close() <a href="/builtin/#error">error</a></pre>

Close closes the Zip file, rendering it unusable for I/O.

<h2 id="Reader">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L15">Reader</a>
    <a href="#Reader">¶</a></h2>
<pre>type Reader struct {
<span id="Reader.File"></span>    File    []*<a href="#File">File</a>
<span id="Reader.Comment"></span>    Comment <a href="/builtin/#string">string</a>
    <span class="comment">// contains filtered or unexported fields</span>
}</pre>


<a id="exampleReader"></a>
Example:

    // Open a zip archive for reading.
    r, err := zip.OpenReader("testdata/readme.zip")
    if err != nil {
        log.Fatal(err)
    }
    defer r.Close()

    // Iterate through the files in the archive,
    // printing some of their contents.
    for _, f := range r.File {
        fmt.Printf("Contents of %s:\n", f.Name)
        rc, err := f.Open()
        if err != nil {
            log.Fatal(err)
        }
        _, err = io.CopyN(os.Stdout, rc, 68)
        if err != nil {
            log.Fatal(err)
        }
        rc.Close()
        fmt.Println()
    }
    // Output:
    // Contents of README:
    // This is the source code repository for the Go programming language.

<h3 id="NewReader">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L61">NewReader</a>
    <a href="#NewReader">¶</a></h3>
<pre>func NewReader(r <a href="/io/">io</a>.<a href="/io/#ReaderAt">ReaderAt</a>, size <a href="/builtin/#int64">int64</a>) (*<a href="#Reader">Reader</a>, <a href="/builtin/#error">error</a>)</pre>

NewReader returns a new Reader reading from r, which is assumed to have the
given size in bytes.

<h3 id="Reader.RegisterDecompressor">func (*Reader) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/reader.go#L112">RegisterDecompressor</a>
    <a href="#Reader.RegisterDecompressor">¶</a></h3>
<pre>func (z *<a href="#Reader">Reader</a>) RegisterDecompressor(method <a href="/builtin/#uint16">uint16</a>, dcomp <a href="#Decompressor">Decompressor</a>)</pre>

RegisterDecompressor registers or overrides a custom decompressor for a specific
method ID. If a decompressor for a given method is not found, Reader will
default to looking up the decompressor at the package level.

<h2 id="Writer">type <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L13">Writer</a>
    <a href="#Writer">¶</a></h2>
<pre>type Writer struct {
    <span class="comment">// contains filtered or unexported fields</span>
}</pre>

Writer implements a zip file writer.

<a id="exampleWriter"></a>
Example:

    // Create a buffer to write our archive to.
    buf := new(bytes.Buffer)

    // Create a new zip archive.
    w := zip.NewWriter(buf)

    // Add some files to the archive.
    var files = []struct {
        Name, Body string
    }{
        {"readme.txt", "This archive contains some text files."},
        {"gopher.txt", "Gopher names:\nGeorge\nGeoffrey\nGonzo"},
        {"todo.txt", "Get animal handling licence.\nWrite more examples."},
    }
    for _, file := range files {
        f, err := w.Create(file.Name)
        if err != nil {
            log.Fatal(err)
        }
        _, err = f.Write([]byte(file.Body))
        if err != nil {
            log.Fatal(err)
        }
    }

    // Make sure to check the error on Close.
    err := w.Close()
    if err != nil {
        log.Fatal(err)
    }

<h3 id="NewWriter">func <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L32">NewWriter</a>
    <a href="#NewWriter">¶</a></h3>
<pre>func NewWriter(w <a href="/io/">io</a>.<a href="/io/#Writer">Writer</a>) *<a href="#Writer">Writer</a></pre>

NewWriter returns a new Writer writing a zip file to w.

<h3 id="Writer.Close">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L65">Close</a>
    <a href="#Writer.Close">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) Close() <a href="/builtin/#error">error</a></pre>

Close finishes writing the zip file by writing the central directory. It does
not (and cannot) close the underlying writer.

<h3 id="Writer.Create">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L205">Create</a>
    <a href="#Writer.Create">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) Create(name <a href="/builtin/#string">string</a>) (<a href="/io/">io</a>.<a href="/io/#Writer">Writer</a>, <a href="/builtin/#error">error</a>)</pre>

Create adds a file to the zip file using the provided name. It returns a Writer
to which the file contents should be written. The file contents will be
compressed using the Deflate method. The name must be a relative path: it must
not start with a drive letter (e.g. C:) or leading slash, and only forward
slashes are allowed. The file's contents must be written to the io.Writer before
the next call to Create, CreateHeader, or Close.

<h3 id="Writer.CreateHeader">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L243">CreateHeader</a>
    <a href="#Writer.CreateHeader">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) CreateHeader(fh *<a href="#FileHeader">FileHeader</a>) (<a href="/io/">io</a>.<a href="/io/#Writer">Writer</a>, <a href="/builtin/#error">error</a>)</pre>

CreateHeader adds a file to the zip archive using the provided FileHeader for
the file metadata. Writer takes ownership of fh and may mutate its fields. The
caller must not modify fh after calling CreateHeader.

This returns a Writer to which the file contents should be written. The file's
contents must be written to the io.Writer before the next call to Create,
CreateHeader, or Close.

<h3 id="Writer.Flush">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L49">Flush</a>
    <a href="#Writer.Flush">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) Flush() <a href="/builtin/#error">error</a></pre>

Flush flushes any buffered data to the underlying writer. Calling Flush is not
normally necessary; calling Close is sufficient.

<h3 id="Writer.RegisterCompressor">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L378">RegisterCompressor</a>
    <a href="#Writer.RegisterCompressor">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) RegisterCompressor(method <a href="/builtin/#uint16">uint16</a>, comp <a href="#Compressor">Compressor</a>)</pre>

RegisterCompressor registers or overrides a custom compressor for a specific
method ID. If a compressor for a given method is not found, Writer will default
to looking up the compressor at the package level.

<a id="exampleWriter_RegisterCompressor"></a>
Example:

    // Override the default Deflate compressor with a higher compression level.

    // Create a buffer to write our archive to.
    buf := new(bytes.Buffer)

    // Create a new zip archive.
    w := zip.NewWriter(buf)

    // Register a custom Deflate compressor.
    w.RegisterCompressor(zip.Deflate, func(out io.Writer) (io.WriteCloser, error) {
        return flate.NewWriter(out, flate.BestCompression)
    })

    // Proceed to add files to w.

<h3 id="Writer.SetComment">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L55">SetComment</a>
    <a href="#Writer.SetComment">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) SetComment(comment <a href="/builtin/#string">string</a>) <a href="/builtin/#error">error</a></pre>

SetComment sets the end-of-central-directory comment field. It can only be
called before Close.

<h3 id="Writer.SetOffset">func (*Writer) <a href="//github.com/golang/go/blob/release-branch.go1.10/src/archive/zip/writer.go#L40">SetOffset</a>
    <a href="#Writer.SetOffset">¶</a></h3>
<pre>func (w *<a href="#Writer">Writer</a>) SetOffset(n <a href="/builtin/#int64">int64</a>)</pre>

SetOffset sets the offset of the beginning of the zip data within the underlying
writer. It should be used when the zip data is appended to an existing file,
such as a binary executable. It must be called before any data is written.


