---
tags:
  - Others
---
# Core principles
A way to represent any number in base-2 numeral system. It consists of ```0``` and ```1```, like:
```
110111010000110011110...
```
The meaning of those zeroes and ones depends on what it is representing. For example, they can represent ```ASCII``` characters, unsigned ```integer``` values, signed ```integer``` values, unsigned ```real``` values, signed ```real``` values...
# Concepts
## Bit
Each digit in the representation is called **bit** (```0``` or ```1```).
## Byte
A group of 8 [[#Bit|bits]]. For instance:
```
01101111
```
## Hexadecimal representation
You can represent any group of 4 bits in HEX code using this translation:
```
1010 => A
1011 => B
1100 => C
1101 => D
1110 => E
1111 => F
```
### Example
```
0111 0101 1001 0001 1110 1101
```
Would be the same as:
```
0x7591ED
```
_Typically, if some binary sequence is represented in HEX code, we write `0x` at the beginning_
# Unsigned integer values
## Translation
To translate unsigned ```integer``` values into binary and vice versa, multiply each [[#Bit]] by its weight. The weight is generically 2<sup>i</sup>, where ```i``` is the [[#Bit]] position. The index goes from ```0``` backwards:
```
Binary digit: 0       1      1      0      1     1     0     0

i:            7       6      5      4      3     2     1     0

weight:       128     64     32     16     8     4     2     1

result:       128*0 + 64*1 + 32*1 + 16*0 + 8*1 + 4*1 + 2*0 + 1*0 = 108
```

# Operations
# AND
It is like a product.

| Bit 1 | Bit 2 | Result |
| ----- | ----- | ------ |
| 0     | 0     | 0      |
| 0     | 1     | 0      |
| 1     | 0     | 0      |
| 1     | 1     | 1      |

## Bitwise
_Bitwise = Bit per bit_
Example:
```
    11001001
AND 00101101
------------
    00001001
```
