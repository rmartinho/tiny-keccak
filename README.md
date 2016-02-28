# tiny-keccak

An implementation of the FIPS-202-defined SHA-3 and SHAKE functions.

[![Build Status][travis-image]][travis-url]

[travis-image]: https://travis-ci.org/debris/tiny-keccak.svg?branch=master
[travis-url]: https://travis-ci.org/debris/tiny-keccak

[Documentation](http://debris.github.io/tiny-keccak/tiny_keccak/index.html)

The `Keccak-f[1600]` permutation is fully unrolled; it's nearly as fast
as the Keccak team's optimized permutation.

## Building

```bash
cargo build
```

## Usage

Add this to your `Cargo.toml`:

```toml
[dependencies]
tiny-keccak = "1.0"
```

and this to your crate root:

```rust
extern crate tiny_keccak;
```

## Example

```rust
extern crate tiny_keccak;
use tiny_keccak::Keccak;

fn main() {
    let mut sha3 = Keccak::new_sha3_256();
    let data: Vec<u8> = From::from("hello");
    let data2: Vec<u8> = From::from("world");

    sha3.update(&data);
    sha3.update(&[b' ']);
    sha3.update(&data2);

    let mut res: [u8; 32] = [0; 32];
    sha3.finalize(&mut res);

    let expected: &[u8] = &[
        0x64, 0x4b, 0xcc, 0x7e, 0x56, 0x43, 0x73, 0x04,
        0x09, 0x99, 0xaa, 0xc8, 0x9e, 0x76, 0x22, 0xf3,
        0xca, 0x71, 0xfb, 0xa1, 0xd9, 0x72, 0xfd, 0x94,
        0xa3, 0x1c, 0x3b, 0xfb, 0xf2, 0x4e, 0x39, 0x38
    ];

    assert_eq!(&res, expected);
}
```

## Benchmarks

Benchmarked with [rust-crypto](https://github.com/DaGenix/rust-crypto) sha3 on:

```
MacBook Pro (Retina, 15-inch, Mid 2015)
2,5 GHz Intel Core i7
16 GB 1600 MHz DDR3
Intel Iris Pro 1536 MB
```

Benchmark code is available [here](https://github.com/debris/tiny-keccak/blob/master/comparison/benches/sha3.rs)

```
running 4 tests
test rust_crypto_sha3_256_input_32_bytes   ... bench:       1,538 ns/iter (+/- 180) = 20 MB/s
test rust_crypto_sha3_256_input_4096_bytes ... bench:      47,135 ns/iter (+/- 8,707) = 86 MB/s
test tiny_keccak_sha3_256_input_32_bytes   ... bench:         698 ns/iter (+/- 113) = 45 MB/s
test tiny_keccak_sha3_256_input_4096_bytes ... bench:      20,772 ns/iter (+/- 2,692) = 197 MB/s
```

Why tiny-keccak is twice faster? It's optimized with:
- [loop unwinding](https://en.wikipedia.org/wiki/Loop_unrolling)
- [removed bounds checking](https://en.wikipedia.org/wiki/Bounds_checking)
