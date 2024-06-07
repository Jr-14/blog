---
title: 'Cryptopals Set-1 Convert Hex to Base64'
date: 2024-05-08T19:15:03+10:00
draft: true
math: 'katex'
tags:
  - math
  - cryptography
  - cryptopals
---

# Introduction
I've been learning Rust with the aid of ['The Rust Book'](https://doc.rust-lang.org/book/), and [Rustlings](https://github.com/rust-lang/rustlings). It's also a good idea to suplement learning by taking on small exercises and projects, so I thought it'd be fun to tackle [Cryptopals](https://cryptopals.com/).

**Disclaimer:** I am no expert in Rust and this will reflect in my code. I'm happy to receive feedback :smile:

# Starting Out
The [first Cryptopal challenge](https://cryptopals.com/sets/1/challenges/1) is to convert the hex string

```
"49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d"
```

into the base64 encoded string

```
"SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t"
```

which is all good and dandy, but with an extra rule. **Always operate on raw bytes, never on encoded strings. Only use hex and base64 for pretty-printing.**

# Converting Hex to Base64

## Using the Standard Library And Crates
I call this the *Cheat Method* since we don't gain any understanding on:
- the underlying datastructure for how strings, characters are represented
- algorithm for converting a hex encoded string into a base64 encoded string

One can easily google search *'How to convert a hex string into base64 in rust'* and easily stumbled upon ***a*** solution. Here's an [example solutions](https://dev.to/nvnx/cryptopals-crypto-challenges-using-rust-convert-hex-to-base64-jkc) :smile:
```rust
use base64;
use hex;

fn main() {
    let hex_string = "49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d";
    let result = "SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t";
    let output = convert_hex_to_base64(hex_string);
    assert_eq!(output, result);
}


pub fn convert_hex_to_base64(hex: &str) -> String {
    base64::encode(hex::decode(hex).unwrap())
}
```

Cool! We used a few external crates and use their API. But how does it work?

## Doing it all from scratch
From here on let's permit ourselves to only use the standard library without any external crates.

### Understanding how characters and strings are represented
As a programmer I think it's important to understand how 1's and 0's are converted into the awesome text that we see on our devices. Here is a very good primer into string encodings - [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/). The Rust book also goes into detail with how [Rust defines a string](https://doc.rust-lang.org/beta/book/ch08-02-strings.html) and its definition is used as the foundation for converting a hex string into a base64 encoded string.

TLDR for what a string is in Rust, there are multiple ***string*** types in Rust; hard-coded strings are the type `str` or in its borrowed form `&str` where its size is known at compile time, whilst the size of `String` cannot be determined at compile time and lives in the heap. `String` type is mutable and owned. Both types `String` and `&str` are UTF-8 encoded.

Consequently a `char` type in Rust is a [Unicode scalar value](https://www.unicode.org/glossary/#unicode_scalar_value), but for the sake of brevity it just means `char` types encode well into UTF-8 and hence it's safe to store `char` in a `str`. It also follows that `char` types are always [four bytes in size](https://doc.rust-lang.org/std/primitive.char.html#representation). However, when a character is part of a `String` or `str` type, the representation for the character is different. [Link to Rust playground code](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=b1e42c055724b92c40c6426c5db4e7da)

```rust
#![allow(unused)]
fn main() {
    let v = vec!['h', 'e', 'l', 'l', 'o'];

    // five elements times four bytes for each element
    assert_eq!(20, v.len() * std::mem::size_of::<char>());

    let s = String::from("hello");

    // five elements times one byte per element
    assert_eq!(5, s.len() * std::mem::size_of::<u8>());
}
```

In this instance, ASCII characters inside a `str` type is equivalent to the size of a `u8` (8 bit unsigned integer) type. Let's have a look how the string `hello` is represented. [Link to Rust playground code](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=d466c0cc39662094d293510619aabbf8)

```rust
#![allow(unused)]
use std::iter::zip;
fn main() {
    let hello_in_bytes = "hello"
        .as_bytes()
        .iter();
    
    zip(hello_in_bytes, "hello".chars())
        .for_each(|(byte, chr)| println!("{:?} - {:b}", chr, byte));

    /// prints to the standard output as:
    /// 'h' -  1101000
    /// 'e' -  1100101
    /// 'l' -  1101100
    /// 'l' -  1101100
    /// 'o' -  1101111
}
```

Rust is able to store each character in a string within a byte (7 bits for each character)! This makes it useful as we can cast characters in a string into `u8`.

### Converting a Hex encoded string into base64 encoded
The very first step is to convert our hex string into its correct integer representation. For example, the character '4' should be converted to a 4 (decimal) and an 'f' or 'F' should be a 15 (decimal). We can do this by grabbing the byte value of the character '0' for numbers, and subtracting it to any character numbers. Likewise, we do the same for lowercase and uppercase characters; grab the byte value of the character 'a' for lowercase and byte value of 'A' for uppercase and subtract it to the corresponding lower or uppercase character, and then we add 10. We add 10 since 'a' or 'A' is the number 10 (decimal) in hexadecimal. 

```rust
/// Calculate the corresponding hex character into a number
pub fn hex_value(c: &u8) -> Result<u8, &'static str> {
    match c {
        b'a'..=b'f' => Ok(c - b'a' + 10),
        b'A'..=b'F' => Ok(c - b'A' + 10),
        b'0'..=b'9' => Ok(c - b'0'),
        _ => Err("Invalid hex character"),
    }
}
```

I opted to wrap the `return` value in a `Result<T, E>` generic (I could have opten for `Option<T>`) as I believe this is correct return type. If we pass a `u8` type that can't be matched to a hexadecimal character, then it should throw an error and let the caller handle that case.

Below is a partial implementation of the `hex_to_base64` function where we print the hexadecimal representation of each byte in the string.

```rust
pub fn hex_to_base64(hex_string: &str) {
    hex_string
        .as_bytes()
        .iter()
        .map(|byte| hex_value(byte).expect("Invalid hex character"))
        .collect::<Vec<u8>>()
        .iter()
        .for_each(|byte| println!("{:?} - {:b}", byte));
}

```

There are multiple algorithms that we can use to convert a Hex encoded string into base64 encoding. The naive solution is to:
- Convert a hex encoded string into an integer (base 10)
- Convert the integer into base64.

But this isn't correct. The [Wikipedia page for Base64](https://en.wikipedia.org/wiki/Base64) illustrates that Base64 converts three octect groups into four encoded 6 bit characters. We know that each character in our string fits into a byte, we now need to come up with an algorithm that 
