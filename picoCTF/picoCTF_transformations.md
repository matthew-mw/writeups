# Transformation | 20 points | Reverse Engineering
## Understanding what the code does
To solve this problem, we start by analyzing the code segment below.
```python
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```
To make it easier to read, I will rewrite the code segment as:
```python
result = []

for i in range(0, len(flag), 2):
    result += chr((ord(flag[i]) << 8) + ord(flag[i + 1]))
    
print(''.join(result))
```
Since `flag[i+1]` was passed to `ord()` without any problems in the snippet below, we can infer that the variable `flag` is of type `string` <sub>[1]</sub>
```python
ord(flag[i+1])
```
We can also infer that the variable `flag` of type `string` can be split into segments of 2 based on the segment below.
```python
for i in range(0, len(flag), 2):
```

In the code snippet below, We:
- shift the ASCII representation of `flag[i]` left by 8 bits <sub>[3]</sub>
- add the ASCII representation of `flag[i+1]` to the result of the left shift
- convert the result to a character using `chr()`
```python
chr((ord(flag[i]) << 8) + ord(flag[i + 1]))
```
## Undoing the transformation

### Proof

To undo the transformation, we have to find the inverse of this manipulation.

To find the original value that was shifted left, we shift the value of the ASCII number of the character right by 8 bits.

This works, as ASCII values that are contained in the flag should all be printable characters found on the keyboard. These characters occupy the ASCII values 32 to 126 inclusive. <sub>[4]</sub>

Let `a` be the ascii representation of `flag[i]`

(32 <= `a` <= 126)

Let `b` be the ASCII representation of `flag[i+1]`

(32 <= `b` <= 126)

Let `n` be the number of digits shifted left/right

n = 8

Let `c` be the result of `a << n + b`

(64 <= `c` <= 32382)

We also know that:

- `c` = ((`a` << `n`) + `b`) = (`a` * (2 ^ `n`) + `b`) <sub>[3]</sub>
- `a` = (`c` >> `n`) = floor(`c` / (2 ^ `n`)) <sub>[3]</sub>
- - If `b` < ((`a` << `n`) \* 2)

*Note that the floor function means that precision **will** be lost due to the right shifts.*

### Solution

#### First character

To find the ASCII code of the first character, we can use the result of

`a` = (`c` >> `n`)

Since we know that:
- `a` has to satisfy the condition (32 <= `a` <= 126)
- `b` has to satisfy the condition (32 <= `b` <= 126)
- `n` = 8

We know that `b` < ((`a` << `n`) \* 2) has to be true for all `a` and `b` that satisfy the conditions above.

So, the ASCII code of the first character of the pair can be expressed as:

`a` = (`c` >> `n`)

#### Second character

To find the ASCII code of the second character, we can use the result of

`c` = ((`a` << `n`) + `b`)

This gives us

`b` = `c` - (`a` << `n`)

## Putting it all together
```python
import requests
scrambled = requests.get("https://mercury.picoctf.net/static/77a2b202236aa741e988581e78d277a6/enc").text
flag = ""
n = 8
for enc in scrambled:
    c = ord(enc)
    a = c >> n
    flag += chr(a)
    b = c - (a << n)
    flag += chr(b)

print(flag)
```

## The flag

This gives us the flag: picoCTF{16_bits_inst34d_of_8_75d4898b}

## References

1 [docs.python.org ord()](https://docs.python.org/3/library/functions.html?highlight=ord#ord)

2 [docs.python.org chr()](https://docs.python.org/3/library/functions.html?highlight=chr#chr)

3 [docs.python.org Shifting Operations](https://docs.python.org/3/reference/expressions.html#shifting-operations)

4 [The US ASCII Character Set](http://www.columbia.edu/kermit/ascii.html)
