---
title: 'Cryptopals S1 - Challenge 1 Convert Hex to Base64'
date: 2024-06-09T18:58:03+10:00
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

Cool! We used a few external crates and used their API, but how does it work?

## Doing it all from scratch
Let's only permit ourselves to use the standard library without any external crates.

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
The very first step is to convert our hex string into its correct integer representation. For example, the character '4' should be converted to a 4 (decimal) and an 'f' or 'F' should be a 15 (decimal). We can do this by grabbing the byte value of the character '0' for numbers, and subtracting it to any character numbers. Likewise, we do the same for lowercase and uppercase characters; grab the byte value of the character 'a' for lowercase and byte value of 'A' for uppercase and subtract it to the corresponding lower or uppercase character, and then we add 10. We add 10 since 'a' or 'A' is the number 10 (decimal) in hexadecimal. We can create a function as such:

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

The `hex_value` function takes a `u8` type and matches it based on a range of byte values. If the byte value is within the range of characters suitable for hexadecimal characters, then the corresponding hexadecimal integer is calculated and wrapped in a `Result<T, E>` generic. If the byte value is not within the range, then it will throw an error and we let the caller handle the error.

Below is a partial implementation of the `hex_to_base64` function where we convert the hexadecimal character into an integer and print the hexadecimal representation of each byte in the string. [Link to Rust playground code](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=cdd806d1baaf88b4716441df991c4a99)

```rust
pub fn hex_to_base64(hex_string: &str) {
    hex_string
        .as_bytes()
        .iter()
        .map(|byte| hex_value(byte).expect("Invalid hex character"))
        .collect::<Vec<u8>>()
        .iter()
        .for_each(|byte| println!("{:?} - {:b}", byte, byte));
}
```

Here is an illustration of the code above for converting a hexadecimal character to its 4 bit representation.

{{< figure
    src="images/bit-representation-of-hex-characters.png"
    alt="Bit representation of hexadecimal characters"
    caption="Bit representation of hexadecimal characters - created in Excalidraw"
>}}

We now have an array of bytes (`u8`) that we can convert into a base64 encoded string. Notice that the entire 8 bit of a `u8` is not used, rather only the first 4 bits are used. This is because hexadecimal fits into 4 bits. To convert from hexadecimal to base64, we take 3 chunks of 4 bits and convert into 2 chunks of 6 bits. The [Wikipedia for Base64](https://en.wikipedia.org/wiki/Base64) illustrates that Base64 chunks the bits into sextet (6 bits) groups and uses the sextets to encode the characters. We can use bit manipulation to convert 3 chunks of 4 bits into 2 chunks of 6 bits, and deal with the remaining bits in the last chunk as padding.

To convert 3 chunks of 4 bits into 2 chunks of 6 bits, we can use bit manipulation to shift the bits into the correct positions.

We calculate the first 6 bit chunk from the following. The first chunk of 4 bits is left shited by 2, and the second chunk of 4 bits is right shifted by 2. We take the sum of the two manipulated bits and this gives us the first 6 bits of the 6 bit chunk. Here is an illustration for the bit manipulation explained above:

{{< figure
    src="images/first-6-bit-chunk-conversion.png"
    alt="First 6 bit chunk conversion of the first two 4 bit hexadecimal characters"
    caption="First 6 bit chunk conversion of the first two 4 bit hexadecimal characters - created in Excalidraw"
>}}

Consequently the second 6 bit chunk is is first calculated by taking the second element of the 4 bit chunk and applying the logical AND operator with the mask `0b11` which is 3 in binary. The result of this operation gives us the first 2 bits, which is then left shifted by 4 bits. Only then do we add the value of the third element of the 4 bit chunk to the result in the previous step. Here is an illustration of the following:

{{< figure
    src="images/second-6-bit-chunk-conversion.png"
    alt="Second 6 bit chunk conversion of the second and third two 4 bit hexadecimal characters"
    caption="Second 6 bit chunk conversion of the second and third two 4 bit hexadecimal characters - created in Excalidraw"
>}}

What if the chunk is not of length 3? In that case we have different approaches for chunk lengths of 1 and 2. For chunk length of 1, we shift left shift the element by 2 bits. For chunk length of 2, we apply the same procedure for a chunk length of 3, except for the calculation of the second 6 bit chunk. In this case, there is no third element in the 4 bit chunk, so we simply take the second element with the appropriate bit manipulation without adding anything to the result.

The final step is to handle the padding. We can do this by taking the length of the final string and dividing it by 4. If the remainder is greater than 0, then we need to add the appropriate number of padding characters to the end of the string. Here the padding character is `'='`.

{{< figure
    src="images/hex-bits-to-base64.png"
    alt="Converting the hexadecimal bits into base64 characters with padding"
    caption="Converting the hexadecimal bits into base64 characters with padding - created in Excalidraw"
    width="600"
>}}


Here is the full implementation of the `hex_to_base64` function. [Link to Rust playground code](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=2020f5c0d7b69ecfab7d2387e18d39c9)
```rust
#![allow(unused)]
fn main() {
    let hex_string = "49276d206b696c6c696e6720796f757220627261696e206c696b65206120706f69736f6e6f7573206d757368726f6f6d";
    let output = "SSdtIGtpbGxpbmcgeW91ciBicmFpbiBsaWtlIGEgcG9pc29ub3VzIG11c2hyb29t";
    let base64_str = hex_to_base64(&hex_string);
    println!("{:?}", base64_str);
    assert_eq!(String::from(output), base64_str);
}

/// Convert a hexadecimal encoded string into base64 encoded string.
pub fn hex_to_base64(hex_string: &str) -> String {
    let base64_chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
    let mut base64_str = hex_string
        .as_bytes()
        .iter()
        .map(|byte| hex_value(byte).expect("Invalid hex character"))
        .collect::<Vec<u8>>()
        .chunks(3)
        .fold(String::from(""), |mut acc, chunk| {
            if chunk.len() == 3 {
                let first_index: usize = ((chunk[0] << 2) + (chunk[1] >> 2)).into();
                let second_index: usize = (((chunk[1] & 3) << 4) + (chunk[2])).into();
                let first_char = base64_chars
                    .chars()
                    .nth(first_index)
                    .expect("No base64 char found");
                let second_char = base64_chars
                    .chars()
                    .nth(second_index)
                    .expect("No bas64 char found");
                acc.push(first_char);
                acc.push(second_char);
            } else if chunk.len() == 2 {
                let first_index: usize = ((chunk[0] << 2) + (chunk[1] >> 2)).into();
                let second_index: usize = ((chunk[1] & 3) << 4).into();
                let first_char = base64_chars
                    .chars()
                    .nth(first_index) 
                    .expect("No base64 char found");
                let second_char = base64_chars
                    .chars()
                    .nth(second_index)
                    .expect("No base64 char found");
                acc.push(first_char);
                acc.push(second_char);
            } else {
                let first_index: usize = (chunk[0] << 2).into();
                let first_char = base64_chars
                    .chars()
                    .nth(first_index) 
                    .expect("No base64 char found");
                acc.push(first_char);
            }
            acc
        });
    let padding_amount = if base64_str.len() % 4 == 0 {
        0
    } else {
        4 - (base64_str.len() % 4)
    };
    for _ in 0..padding_amount {
        base64_str.push('=');
    }
    base64_str
}

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

Here you can [view the full respoitory in Github](https://github.com/Jr-14/cryptopals-rust)

# Closing Remarks
Uncovering the magic behind how to convert a hex encoded string into a base64 encoded string has been satisfying.
Majority of Cryptopal posts for this challenge leverages existing libraries, but I believe this challenge is best tackled
when you attempt to understand how encoding and decoding works for both hex and base64.
