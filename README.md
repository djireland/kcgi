## Synopsis

kcgi is an open source CGI and FastCGI library for C/C++ web applications.
It is minimal, secure, and auditable; and fits within your
[BCHS](https://learnbchs.org) software stack.

This repository consists of bleeding-edge code between versions: to keep
up to date with the current stable release of kcgi, visit the [kcgi
website](https://kristaps.bsd.lv/kcgi).
The website also contains canonical installation, deployment, and usage
documentation.
This page describes using the bleeding-edge version of the system.

## Example

Implementing a CGI or FastCGI application with kcgi is easy (for values
of easy strictly greater than "knows C/C++").
Specify the pages recognised by the application and the known HTML form
inputs.
kcgi parses the request and can manage output.

```c
#include <sys/types.h> /* size_t, ssize_t */
#include <stdarg.h> /* va_list */
#include <stddef.h> /* NULL */
#include <stdint.h> /* int64_t */
#include <kcgi.h>
 
int main(void) {
  struct kreq r;
  const char *page = "index";
  if (khttp_parse(&r, NULL, 0, &page, 1, 0) != KCGI_OK)
	return 0;
  khttp_head(&r, kresps[KRESP_STATUS],
	"%s", khttps[KHTTP_200]);
  khttp_head(&r, kresps[KRESP_CONTENT_TYPE], 
	"%s", kmimetypes[KMIME_TEXT_PLAIN]);
  khttp_body(&r);
  khttp_puts(&r, "Hello, world!\n");
  khttp_free(&r);
  return 0;
}
```

For a fuller example reflecting the repository sources, see
[sample.c](https://github.com/kristapsdz/kcgi/blob/master/sample.c)
or, for C++,
[samplepp.cc](https://github.com/kristapsdz/kcgi/blob/master/samplepp.cc).

## Installation

To use the bleeding-edge version of kcgi (instead of from your system's
packages or a stable version), the process it the similar as for source
releases.

Begin by cloning or downloading.  Then configure with `./configure`,
compile with `make` (BSD make, so it may be `bmake` on your system),
then `sudo make install` (or using `doas`).  To install in an
alternative directory to `/usr/local`, set the `PREFIX` variable when
you run `configure`.  (See the
[configure](https://github.com/kristapsdz/kcgi/blob/master/configure)
script for details.)

```sh
./configure
make
doas make install
```

On some Linux machines:

```sh
./configure LDADD='-lm'
bmake
doas bmake install
```

Or FreeBSD:

```sh
./configure LDADD='-lmd'
make
doas make install
```

You can also change the default value in the
[Makefile](https://github.com/kristapsdz/kcgi/blob/master/Makefile).

## API Reference

For the repository version of kcgi, the locally-installed manpages are
the canonical source of information.  (The web-site reflects the latest
release, which may be older than what you have.)

```sh
man kcgi
apropos kcgi
```

This assumes that kcgi has been installed in a path recognised by your
manpage reader.
If you're using an alternative installation path, you may need to edit
`/etc/man.conf` or other configuration file appropriate to your system.

## Tests

It's useful to run the installed regression tests on the bleeding edge
sources.
(Again, this uses BSD make, so it may be `bmake` on your system.)

```sh
make regress
```

The system contains a full regression suite and is also built to work
with [AFL](http://lcamtuf.coredump.cx/afl/).
To run some of the bundled tests, use the binaries compiled into the
`afl` directory.
(Again, this uses BSD make, so it may be `bmake` on your system.)

```sh
make afl
cd afl
afl-fuzz -i in/urlencoded -o out -- ./afl-urlencoded
```

## License

All sources use the ISC (like OpenBSD) license.
See the [LICENSE.md](LICENSE.md) file for details.
