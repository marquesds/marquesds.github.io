---
title: Introduction to bitshift operations
author: lucas
date: 2023-01-26 20:30:00 -0300
categories: [Blogging, Tutorial]
tags: [binary, bitshift, math]
---

# Introduction
Bitshift is the action of shift a bit from one position to another in order to multiply or divide a binary number. For example, if I want to perform a multiplication on number `0001` I'll shift it one bit to the left, and the result will be `0010`. To understand it better, let's remember how we are used to multiply numbers.

# Base 10
We are used to perform mathematical operations on top of the Base 10, which has the following ten numbers: `0, 1, 2, 3, 4, 5, 6, 7, 8, 9`. When we want to multiply one number to another, the result will be composed by one or more of those ten numbers (e.g. `5 * 7 = 35`). Multiplying or dividing numbers by `10` will give you interesting results:
- `10 * 10 = 100`;
- `509 * 10 = 5090`;
- `1000 / 10 = 100`;

Let's vizualize it in another way, filling the "blank" spaces with zero:
- `0010 * 10 = 0100`;
- `0509 * 10 = 5090`;
- `1000 / 10 = 0100`;

As you can see, multiplying or dividing a Base 10 number by `10` is the same as shifting this number to the left (multiplication) or to the right (division). You can do something similar with binary numbers.

# Base 2
While on Base 10 we can operate with `10` numbers, as you can conclude, the Base 2 let us operate only with `2` numbers: `0, 1`. When you think about electronic devices such as a computer this Base is very useful, because it can represent an electrical sign set as `ON` or `OFF` (turning a bit on or off - `0` or `1` - respectively).

Performing mathematical operations over Base 2 numbers follows the same logic as with Base 10 numbers, but with Base 10 we have a richier representation. For example, `0001 + 0001 = 0010` (which is `2` on Base 10), because since we only have two numbers, we need more space (more bits) to represent the whole result.

Each bit can contain only `0` or `1`, so we can conclude that the maximum number that the first bit can hold is `2ˆ0` (which, of course, is `1`), the second bit can hold `2ˆ1 + 2^0 = 3`, the third bit can hold `2^2 + 2^1 + 2^0 = 7`, and so on. So, every time we add more bits we can hold more numbers. For example, the maximum number a binary with four bits can hold is `2^3 + 2^2 + 2^1 + 2^0 = 15`, so `1111 = 15`:

|:---:|---|:---:|---|:---:|---|:---:|
| `1` |   | `1` |   | `1` |   | `1` |
|`2^3`| + |`2^2`| + |`2^1`| + |`2^0`|

# Bitshifting
As multiplying or dividing a Base 10 number by `10` the result is a number shifted to the left or to the right, if we multiply or divide a Base 2 number by `2^N` (two to the power of number `N`) the result will be a bit shifted to the left or to the right as well. The syntax that most programming language uses to represent this operation is:
- Bitshift left (multiplication): `<<`;
- Bitshift right (division): `>>`;

Examples:
- `0001 << 2^1 = 0010`, because `1 * 2 = 2`;
- `0100 >> 2^1 = 0010`, because `4 / 2 = 2`;
- `1000 >> 2^2 = 0010`, because `8 / 4 = 2`;

The bitshift operations are usefull especially if you are programming an old processor, which don't have the multiplication or division operation on it, so you can avoid do a bunch of additions or subtractions in order to achieve a result. But, for most cases you won't need it.

If you want to learn more about it, take a look at [this course](https://pikuma.com/courses/bit-shifting-operator-tutorial){:target="_blank"}, the author also explain signed and unsigned operations.

Thanks for reading this post! Bye 👋🏾
