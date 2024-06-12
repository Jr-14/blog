---
title: 'Cryptopals S1 - Challenge 2 Fixed Xor'
date: 2024-06-12T20:26:11+10:00
draft: true
math: 'katex'
tags:
  - math
  - cryptography
  - cryptopals
---

# Introduction
I'm still learning rust :smile: but here is Challenge 2 for [Cryptopals Set 1 - Fixed
XOR](https://cryptopals.com/sets/1/challenges/2). I'd like to iterate that
I'll only be using the standard library and no external crates. Consequently, I'll permit to myself to use previous
completed Cryptoplal challenge implementations.

# Starting Out
Majority of the implementation for this challenge has been completed in the [First Cryptopal Challenge]({{< ref "cryptopals-set1-hex-to-base64/index.md" >}} "Interesting").

The main implementation that I'll be using is converting a Hexadecimal character into its decimal form.

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

```rust
pub fn fixed_xor(hex_string_one: &str, hex_string_two: &str) -> String {
    let string_one_bits: Vec<u8> = hex_string_one
        .as_bytes()
        .iter()
        .map(|byte| challenge1::hex_value(byte).unwrap())
        .collect();
    let string_two_bits: Vec<u8> = hex_string_two
        .as_bytes()
        .iter()
        .map(|byte| challenge1::hex_value(byte).unwrap())
        .collect();
```
