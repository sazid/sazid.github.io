---
---


**TLDR; use `path.Join` when working with `io/fs.FS` instead of `filepath.Join`.
Always read the docs before you start using some the codes - specially if you're
dealing with os specific resources.**

Quote from doc:

> Note that paths are slash-separated on all systems, even Windows. Paths
containing other characters such as backslash and colon are accepted as valid,
but those characters must never be interpreted by an FS implementation as path
element separators. - https://golang.org/pkg/io/fs/#ValidPath

---

### Experience:

I have been recently working on https://git.io/node_manager this project mostly
from Linux. Some of the code uses the newly introduced (1.16) `io/fs` package -
a read-only abstraction of a filesystem. There's around 30 unit/integration
tests between the different parts of the module and all of them were passing.

However, as I pulled the code on my Windows system today and re-ran the whole
test suite, I quickly discovered that some of the tests were failing - the ones
that interacted with the `io/fs.FS`. Debugging through the code, I found that
`filepath.Join` was joining the path segments with `\`, I suddenly remembered
that `io/fs` works with only `/` (forward slashes, even on Windows). Had I not
read the doc before, this may have cost me a few hours of useless fiddling
around/debugging.

---

**PS**. I highly recommend trying out the `fstest` package as well if you're
testing `io/fs` stuffs. And do use the `io/fs` package if all you're doing is
reading from files/dirs. This is such a nice abstraction - the backend
filesystem can live on a single file/local filesystem/database/ftp/cloud
storages/s3/etc.

More reading:

* `path.Join` - "The path package should only be used for paths separated by
  forward slashes, such as the paths in URLs. This package does not deal with
  Windows paths with drive letters or backslashes; to manipulate operating
  system paths, use the path/filepath package." - https://golang.org/pkg/path/

* `filepath.Join` - "The filepath package uses either forward slashes or
  backslashes, depending on the operating system. To process paths such as URLs
  that always use forward slashes regardless of the operating system, see the
  path package." - https://golang.org/pkg/path/filepath/