# `range-split`

A subset of the UNIX `split` command built with a PRNG for creating files of differening and deterministic sizes, seeded from the input.

## Quick start

Here's the output of `prng_split --help`

```sh
Usage: prng_split [OPTIONS] <RANGE> [FILE]

Arguments:
  <RANGE>
          The range of file sizes that can be generated. The last generated file has no lower bound.

          [n,N] (n,N) [n,N) (n,N]

  [FILE]
          The file to read contents from. If omitted, defaults to standard input

Options:
  -p, --prefix <PREFIX>
          the path prefixed to the start of the generated file. Can be a directory or a path

          [default: ""]

  -f, --factor <FACTOR>
          How many characters file names will be. `aaaa` is the default start when set to `4`.

          When this overflows, it will be `zzzz[a-z]` then `zzzzz[a-z]`.

          [default: 4]

  -v, --verbose...
          Increase logging verbosity

  -q, --quiet...
          Decrease logging verbosity

  -h, --help
          Print help (see a summary with '-h')
```

## Use cases

### Hosting large files in a repository

There's a bit of fluffling around but this can be used to host large files in GitHub without them knowing.

```sh
#  archive, compress, split

INPUT_DIR="path/to/big/directory"
PREFIX=".parts/"
RANGE="(0KB,50MB)" # 50MB is GitHub recommended max size.
tar cf - "$INPUT_DIR" | xz -9 -c | range-split -vvvv --factor 16 --prefix "$PREFIX" "$RANGE"
```

```sh
# concatenate, uncompress, unarchive

cat "$@" | xz -d -c | tar xf -
```

## Nix (Flakes)

This library is available as a flake.

```nix
{
    inputs = {
        range-split.url = "github:waynevanson/elevated-cycling";
    };

    outputs = {}: {

    };
}
```
