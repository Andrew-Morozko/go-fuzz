# go-fuzz
Executes go fuzz test

It is intended to be used in conjunction with the `Go list fuzz targets` action, see <https://github.com/Andrew-Morozko/go-fuzz-example>.

* Runs a single fuzz func
* If fuzzer fails, creates an issue with the encrypted patch file, containing the failing test case and fuzzer log
* Patch file is encrypted with the PGP key contained in the given file (default: ./SECURITY.md) and is uploaded as an artifact.
* Expects the project to be checked out and to have golang installed.
* Requires the `issues: write` permission to create an issue.
* Tested on `ubuntu-latest` runner.

## Example
```yml
jobs:
  fuzz:
    name: Fuzz func
    runs-on: ubuntu-latest
    permissions:
      issues: write
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: "stable"
      - name: Fuzz
        uses: ./fuzz
        with:
          # Required inputs
          # Fuzz test to run
          func: FuzzDivide
          # Function's package
          package: github.com/Andrew-Morozko/go-fuzz-example/divide
          # Time to run the fuzzer using go duration format
          fuzztime: 5m # NOTE: GitHub hosted runners terminate after 6 hours

          # Optional inputs
          # Encrypt the patch file with the pgp key from the given file
          # Default: ./SECURITY.md
          pgp-key-file: "./my-pgp-key.pub"
          tags: "foo,bar"
          # Time to run the fuzzer minimizer using go duration format
          fuzzminimizetime: 20m
```