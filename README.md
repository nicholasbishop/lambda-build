# lambda-build

[![crates.io](https://img.shields.io/crates/v/lambda-build.svg)](https://crates.io/crates/lambda-build)
[![Documentation](https://docs.rs/lambda-build/badge.svg)](https://docs.rs/lambda-build)

Build a Rust project in a container for deployment to AWS Lambda.

This project is a thin wrapper around the excellent
[lambda-rust](https://github.com/softprops/lambda-rust) project, which
provides "a faithful reproduction of the actual AWS [...] Lambda
runtime environment" with the stable Rust toolchain installed.

The lambda-build crate expands on lambda-rust in a few ways:
1. It downloads the lambda-rust repo and builds a specific branch,
   tag, or commit instead of using a build from Docker hub. This is
   useful because the lambda-rust repo is sometimes updated without a
   new tag being pushed to Docker hub.
2. It builds and runs the lambda-rust container with all the necessary
   options. There are a number of volumes that need to get mounted in
   the right place for caching to work.
3. The zip files are created with unique names that include the date
   and a partial sha256 hash. This is convenient when uploading to S3
   so that new packages don't overwrite old ones.
   
This crate only handles building the project locally. It does not
interact with any AWS services.

Both a library and an executable are provided. The executable is a
very thin wrapper around the library.

## Installation

```
cargo install lambda-build
```

## Usage

In the common case you should be able to just run `lambda-build` in
the directory of the project you want to build. You can also pass an
explicit directory to build. By default the master branch of
`lambda-rust` is used; a different one can be set with `--rev`.

On successful completion, the packaged zip file(s) will be written to
the `lambda-target` directory. There is also a `lambda-target/latest`
file that contains the names of all the zip files written.

```
lambda-build [<project>] [--repo <repo>] [--rev <rev>] [--cmd <cmd>]

Build the project in a container for deployment to Lambda.

Options:
  --repo            lambda-rust repo (default:
                    https://github.com/softprops/lambda-rust)
  --rev             branch/tag/commit from which to build (default: master)
  --cmd             container command (default: docker)
  --help            display usage information
```

## Related projects

- [lambda-rust](https://github.com/softprops/lambda-rust): "a faithful
  reproduction of the actual AWS [...] Lambda runtime environment" with
  the stable Rust toolchain installed
- [cargo-aws-lambda](https://github.com/vvilhonen/cargo-aws-lambda):
  "dependency free cargo subcommand for cross-compiling, packaging and
  deploying code quickly to AWS Lambda"
