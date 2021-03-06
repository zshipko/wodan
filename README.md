# Wodan

Wodan is a flash friendly, safe and flexible
filesystem library for Mirage OS.

Currently the basic disk format is implemented.

## Paper

This explains some of the design choices behind Wodan.

[ICFP 2017](https://icfp17.sigplan.org/event/ocaml-2017-papers-wodan-a-pure-ocaml-flash-aware-filesystem-library)

## Building, installing and running

Wodan requires Opam, Mirage 3, and OCaml 4.06.

An opam switch with flambda is recommended for performance reasons.

```
opam switch 4.06.1+fp+flambda
```

### Building the library

```
opam install -y jbuilder mirage ctypes-foreign
(cd runner; mirage configure --target unix)
jbuilder external-lib-deps --missing @install runner/main.exe cli/wodanc.exe # Follow the opam instructions
make
```

## CLI usage

```
_build/default/cli/wodanc.exe --help
```

At the moment the CLI supports creating filesystems, dumping and restoring data into them.

### Running tests

```
_build/default/runner/main.exe
```

By default, tests will run using a ramdisk.
To use a file:

```
(cd runner; mirage configure --target unix --block=file)
make
touch runner/disk.img
fallocate -l$[16*2**20] -z runner/disk.img
_build/default/runner/main.exe
```

The file must be cleared (using fallocate) before each run.
This is because tests create a new filesystem instance which,
until we get discard support, requires a clear disk.

### Running American Fuzzy Lop (AFL)

This requires OCaml compiled with AFL support.

```
opam switch 4.06.1+afl
afl-fuzz -i afl/input -o afl/output -- \
  _build/default/cli/wodanc.exe fuzz @@
```

