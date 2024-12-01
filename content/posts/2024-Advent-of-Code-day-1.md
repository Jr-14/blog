---
title: '2024 Advent of Code Day 1'
date: 2024-12-01T19:25:36+11:00
draft: true
math: 'katex'
tags:
  - 'Advent of Code'
---

It's that time of the year where we can do Advent of Code 2024!

# Day 1 Part 1
The [Advent of Code 2024 Day 1 Challenge](https://adventofcode.com/2024/day/1) (Part 1) gently introduces the idea of
pairing the same ordered elements in two lists.

> *To find out, pair up the numbers and measure how far apart they are. Pair up the smallest number in the left list
> with the smallest number in the right list, then the second-smallest left number with the second-smallest right
> number, and so on.*

## Solution
Given that $n$ is the number of pairs, we can solve the given problem in $O(nlog_n)$ time with the following sudo code.

```
// O(n) time - read the file input for the problem into a vector of tuple
let tuple_pairs = read_from_file(input_path);

// O(n) time - Place each tuple value into their corresponding left or right location array
let left_locations = [];
let right_locations = [];
for (i = 0; i < tuple_pairs.length; i++) {
    left_locations[i] = tuple[0];
    right_locations[i] = tuple[1];
}

// O(n log(n)) time - Sort each array in place.
left_locations.sort()
right_locations.sort()

// O(n) time - Array elements are now sorted (both arrays have to be sorted in ascending or descending order).
// Loop through each location pair at index i, take the absolute difference between the two pairing and add it
// to the total distance
let total_distance = 0;
for (i = 0; i < left_locations.length; i++) {
    total_distance += abs(left_locations[i] - right_locations[i]);
}

return total_distance;
```


Here is [my Rust implementation](https://github.com/Jr-14/advent-of-rust/blob/main/src/aoc2024/day1.rs#L6)
```Rust
use std::{error::Error, fs};

const DAY_1_2024_INPUT: &'static str = "./inputs/2024/day1";

pub fn calculate_distances(contents: &str) -> Result<i32, Box<dyn Error>> {
    let mut left_loc: Vec<i32> = Vec::new();
    let mut right_loc: Vec<i32> = Vec::new();
    for line in contents.lines() {
        let v: Vec<&str> = line.split("   ").collect();
        let left_dist: i32 = v[0].parse()?;
        let right_dist: i32 = v[1].parse()?;
        left_loc.push(left_dist); 
        right_loc.push(right_dist); 
    }

    left_loc.sort();
    right_loc.sort();

    let mut sum = 0;
    let total_dist_iter = left_loc.iter().zip(right_loc.iter());
    for dist_pair in total_dist_iter {
        let distance = (dist_pair.0 - dist_pair.1).abs();
        sum += distance;
    }

    Ok(sum)
}

pub fn run_day_1_part_1() {
   let contents = fs::read_to_string(DAY_1_2024_INPUT) .unwrap_or_else(|err| {
        eprint!("Problem reading input for AoC Day 1 Part 1 {}", err);
        std::process::exit(1);
    });
    
    let distance = match calculate_distances(&contents) {
        Ok(dist) => dist,
        Err(error) => panic!("Problem running AoC 2024 Day 1 - {:?}", error)
    };

    println!("AoC 2024 Day 1 Part 1 answer is {:?}", distance);
}
```
