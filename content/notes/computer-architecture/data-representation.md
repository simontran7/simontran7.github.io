# Data Representation

## Number Systems

### Decimal System

The **Decimal System** is a number system that uses base 10, characterized by two fundamental properties:
- Each digit position can hold one of 10 unique values (0 through 9), where values greater than 9 require carrying to an additional digit position to the left.
- Each digit's position determines its contribution to the overall value through positional notation. Digits are labeled from right to left as $d_0, d_1, d_2, \dots, d_{N - 1}$ where each successive position represents ten times the value of the position to its right.

More formally, any $N$-digit number can be expressed using **positional notation** as:

$$
(d_{N-1} \cdot 10^{N-1}) + (d_{N-2} \cdot 10^{N-2}) + \dots + (d_2 \cdot 10^2) + (d_1 \cdot 10^1) + (d_0 \cdot 10^0)
$$

### Binary System

**Binary** is a number system that uses base 2, where:

- Each digit position (called a **bit**) can hold one of two unique values: 0 or 1. Values greater than 1 require carrying to an additional bit position to the left.
- Each bit's position determines its contribution through powers of 2: $2^{N-1}, \dots, 2^1, 2^0$.

#### Binary Groupings

- **Byte**: 8 bits (the smallest addressable unit of memory)
- **Nibble**: 4 bits (half a byte)
- **Word**: The natural data width of a CPU (typically 32 or 64 bits)
- **Most Significant Bit (MSB)**: The leftmost bit
- **Least Significant Bit (LSB)**: The rightmost bit

### Hexadecimal System

**Hexadecimal** uses base 16, where:

- Each digit position can hold one of 16 unique values, such that:
  - 0 to 9 are represented as is.
  - 10 to 15 are represented as `A` to `F`.
- Each digit's position determines its contribution through powers of 16: $16^{N-1}, \dots, 16^1, 16^0$.

> [!NOTE]
> Binary numbers are typically prefixed with `0b`, while hexadecimal numbers with `0x`.

## Converting Between Number Systems

### Decimal to Any Base (Division method)

1. Repeatedly divide the decimal number by the target base
2. Record the remainder at each step
3. Continue until the quotient becomes 0
4. Read the remainders from bottom to top to get the result

### Any Base to Decimal (Addition of Positional Notation)

For a number with digits $d_{N - 1}, d_{N - 2}, \dots, d_1, d_0$ in base $B$, the converted number to base $C$ is calculated using the following formula:

$$
C = (d_{N-1} \cdot B^{N-1}) + (d_{N-2} \cdot B^{N-2}) + \dots + (d_1 \cdot B^1) + (d_0 \cdot B^0)
$$

### Binary to Hexadecimal

1. Group binary digits into sets of 4 bits from right to left
2. Pad the leftmost group with leading zeros if necessary
3. Convert each 4-bit group to its hexadecimal equivalent using the table below

### Hexadecimal to Binary

1. Convert each hexadecimal digit to its 4-bit binary equivalent using the table below
2. Concatenate all binary groups

| Hex | Binary | Decimal |
| --- | ------ | ------- |
| 0   | 0000   | 0       |
| 1   | 0001   | 1       |
| 2   | 0010   | 2       |
| 3   | 0011   | 3       |
| 4   | 0100   | 4       |
| 5   | 0101   | 5       |
| 6   | 0110   | 6       |
| 7   | 0111   | 7       |
| 8   | 1000   | 8       |
| 9   | 1001   | 9       |
| A   | 1010   | 10      |
| B   | 1011   | 11      |
| C   | 1100   | 12      |
| D   | 1101   | 13      |
| E   | 1110   | 14      |
| F   | 1111   | 15      |

## Computer Data Units

### Memory and Storage Capacities

| Unit            | Value                                                    |
| --------------- | -------------------------------------------------------- |
| 1 KiB (Kibibyte) | $2^{10}$ bytes (1,024 bytes)                         |
| 1 MiB (Mebibyte) | $2^{20}$ bytes (1,048,576 bytes)                     |
| 1 GiB (Gibibyte) | $2^{30}$ bytes (1,073,741,824 bytes)                 |
| 1 TiB (Tebibyte) | $2^{40}$ bytes (1,099,511,627,776 bytes)             |
| 1 PiB (Pebibyte) | $2^{50}$ bytes (1,125,899,906,842,624 bytes)         |
| 1 EiB (Exbibyte) | $2^{60}$ bytes (1,152,921,504,606,846,976 bytes)     |

### Data Transfer Units

| Unit           | Value                  |
| -------------- | ---------------------- |
| 1 Kilobit (Kb) | 1,000 bits             |
| 1 Megabit (Mb) | 1,000,000 bits         |
| 1 Gigabit (Gb) | 1,000,000,000 bits     |
| 1 Terabit (Tb) | 1,000,000,000,000 bits |

## Character Representation

### ASCII

The **American Standard Code for Information Interchange (ASCII)** is a character encoding standard which uses 7 bits per character.

#### Types of ASCII Characters

- Non-printable characters
  - Control characters, from `0x00` to `0x1F`
  - Delete character, `0x7F`
- Printable characters, from `0x20` to `0x7E`

#### ASCII Table

[Searchable ASCII Table](https://www.rapidtables.com/code/text/ascii-table.html)

### Unicode

**Unicode** is a character set standard that assigns a unique hexadecimal identifier, called a **code point**, to every character across all writing systems. These code points follow the format `U+<....>`. It is also a superset of ASCII.

#### Code Point Categories

- **Surrogate code points**: Code points in the range `U+D800` to `U+DFFF`, which are reserved for use in UTF-16 encoding
- **Scalar values**: All other Unicode code points (excluding surrogate code points)

#### Character Encoding Standards

Unicode defines three primary character encoding standards for converting code points into bytes for storage and transmission. These standards use **code units** as their basic storage elements to represent characters.

- UTF-8
  - **Code unit size**: 8 bits (1 byte)
  - **Character representation**: 1 to 4 code units per character
  - **Encoding type**: Variable-length encoding
- UTF-16
  - **Code unit size**: 16 bits (2 bytes)
  - **Character representation**: 1 code unit, or 2 code units for surrogate code points, and we call these 2 code units a **surrogate pair**
- UTF-32
  - **Code unit size**: 32 bits (4 bytes)
  - **Character representation**: Exactly 1 code unit per character

> [!NOTE]
> Code points can be encoded in another encoding scheme. However, when there is equivalent Unicode code point in the other encoding scheme, the character will appear as a �.

> [!NOTE]
> Since UTF-16 and UTF-32 stores multi-byte code points, characters whose Unicode code points fall in the ASCII range (`U+0000` to `U+007F`) gets zero-padded. This leads to *both* big-endian and little-endian valid orderings.
> For instance, "Hello", corresponding to `U+0048 U+0065 U+006C U+006C U+006F`, which can represented as `00 48 00 65 00 6C 00 6C 00 6F` (big-endian) or `48 00 65 00 6C 00 6C 00 6F 00` (little-endian).
> To help decoders detect the byte order, Unicode introduced the **Byte Order Mark** `U+FEFF` which encodes as `FE FF` in big-endian and reads as `FF FE` in little-endian. Modern systems that do use UTF-16 typically just assume little-endian and skip the BOM entirely, which is its own subtle gotcha.

### Grapheme clusters

A **grapheme cluster** represents what users typically think of as a "character", that is, the smallest unit of written language that has semantic meaning.

#### Example

The grapheme clusters in the Hindi word `"क्षत्रिय"` are `["क्ष", "त्रि", "य"]`, where each cluster can comprise multiple Unicode code points:
- The first grapheme `"य"` corresponds to a Unicode single code point, yet
- The third grapheme `"क्ष"` is a conjunct consonant formed from three code points: `"क"` (`U+0915`), `"्"` (`U+094D`), and `"ष"` (`U+0937`), which combine to create one visual unit.
- The second grapheme `"त्रि"` is even more complex, consisting of four code points: `"त"` (`U+0924`), `"्"` (`U+094D`), `"र"` (`U+0930`), and the vowel sign `"ि"` (`U+093F`), where the vowel mark appears visually before the consonant cluster despite following it in the Unicode sequence.

> [!NOTE]
> The example above highlights whysimply counting Unicode code points does not _always_ correspond to what users perceive as individual characters!

## Integer Representation

### Unsigned Integer Representation

**Unsigned integers** use all available bits to represent positive values (including zero). No bit is reserved for a sign, allowing for a larger range of positive values compared to signed integers of the same bit width.

An $N$-bit unsigned integer can take up values in the range of $[0, 2^N - 1]$.

### Signed Integer Representation

**Signed integers** reserve one bit (typically the MSB) to indicate sign, reducing the range of representable positive values but enabling representation of negative numbers.

**Two's complement** is the most common encoding system to represent signed integers. A number encoded under two's complement has its MSB exclusively as a sign bit:
- $\text{MSB} = 0$: positive number (or zero)
- $\text{MSB} = 1$: negative number

An $N$-bit unsigned integer using two's complement can take up values from $-2^{N-1}$ to $2^{N-1} - 1$.

To represent a positive number using two's complement, no change is required.

To represent a negative number using two's complement:
1. Start with the binary representation of the positive number and toggle all the bits
2. Add 1 to the result from step 1

To understand why this works, think of these steps as a way of finding the additive inverse $-b$ such that $b + (-b) = 1000\dots0$. We target $1000\dots0$ ($n + 1$ bits wide) rather than $0000\dots0$ because in a $n$ fixed-width register, the leading $1$ is discarded as overflow, making them equivalent. It also sidesteps the problem with one's complement, where $b + \tilde{b} = 1111\dots1$ introduces a "negative zero" ($1111\dots1$) alongside the usual $0000\dots0$.

Step 1 exploits the fact that toggling all the bits of $b$ produces a number $\tilde{b}$ such that every bit position sums to $1$, giving $b + \tilde{b} = 1111\dots1$. Step 2 then adds $1$ to both sides of the equation: the right-hand side carries all the way through, flipping $1111\dots1$ into $1000\dots0$ (with the leading $1$ overflowing out of the register), and the left-hand side tells us the additive inverse is $\tilde{b} + 1$.

> [!NOTE]
> When converting from binary to decimal, if the MSB is a 1, treat it as a negative number, then proceed as discussed in the "Any Base to Decimal" section.

## Floating-Point Representation

### IEE 754 Structure

For some number $x$:

$$
x = (-1)^\text{sign} \times (\text{integer}.\text{fraction})_2 \times 2^\text{actual exponent}
$$

- Single precision (32 bits):
```
| 1 bit | 8 bits         |           23 bits                   |
  sign    biased exponent            fraction
```
- Double precision (64 bits):
```
| 1 bit |     11 bits      |             52 bits                |
  sign    biased exponent               fraction
```
- Quadruple precision (128 bits):
```
| 1 bit |     15 bits      |             112 bits                |
  sign    biased exponent               fraction
```

> [!note]
> It may not be possible to store a given $x$ exactly with such a scheme whenever the actual exponent is outside of the possible range, or if the fraction field can't fit in the allocated number of bits (i.e., say for single precision, bits 24 and bits 25, where bit 0 is the implicit integer, are ones)

### Fields

- **Signed field**: Represents the positivity, either 0 (for positive) or 1 (for negative).
- **Biased exponent field**: Determines the power of 2 by which to scale the fraction. The exponent is biased, meaning we add a constant to the actual exponent. The general formula is $2^{\text{bits allocated for biased exponent} - 1} - 1$.
    - Single precision bias: $2^{8 - 1} - 1 = (127)_{10}$
    - Double precision bias: $2^{11 - 1} - 1_{10} = (1023)_{10}$
    - Quadruple precision bias: $2^{15 - 1} - 1 = (16383)_{10}$
    - Range of the actual exponents: $[1 - \text{bias}, \text{bias}]$
- **Fraction field**: Represents the digits after the decimal point.

### Normal Numbers

$$
x = (-1)^\text{sign} \times (1.\text{fraction})_2 \times 2^\text{actual exponent}
$$
```
| sign = any | exponent != 00000000 or exponent != 11111111 | fraction = any
```

### Special Numbers

#### $+\infty$ and $-\infty$
```
| sign = any | exponent = 11111111 | fraction = 000...0 |
```

#### NaN

Key properties:
- Any operation with $\text{NaN}$ produces $\text{NaN}$
- $\text{NaN}$ is never equal to anything, including itself

Types of $\text{NaN}$:
- Quiet $\text{NaN}$: silently propagates $\text{NaN}$ through calculations
- Signalling $\text{NaN}$: triggers an exception or error when encountered in operations

**Quiet $\text{NaN}$**
```
| sign = any | exponent = 11111111 | fraction = 1<no restriction> |
```

**Signalling $\text{NaN}$**
```
| sign = any | exponent = 11111111 | fraction = 0<no restriction> |
```

#### 0
```
| sign = any | exponent = 00000000 | fraction = 000000...0 |
```

#### Denormalized (Subnormal) Numbers

$$
x = (-1)^\text{sign} \times (0.\text{fraction})_2 \times 2^\text{smallest possible actual exponent}
$$
```
| sign = any | exponent = 00000000 | fraction != 000...0 |
```

> [!note]
> $+\infty$ and $-\infty$, $+0$ and $-0$ are not interchangeable!

### Converting Decimal to IEEE 754

To convert $12.375_{10}$ to single precision IEEE 754:

**Step 1: Convert to binary**

$$
12.375_{10} = 1100.011_2
$$

**Step 2: Determine the sign**

Since it's a positive number, the sign bit is 0.

**Step 3: Normalize the fraction**

$$
1100.011_2 = 1.100011_2 \times 2^3
$$

The normalized fraction, padded to 23 bits, is: $10001100000000000000000_2$

**Step 4: Calculate the biased exponent**

$$
\text{biased exponent} = \text{actual exponent} + \text{bias} =  3 + 127 = 130 = 10000010_2
$$

**Step 5: Combine all fields**
```
| Sign | Exponent | Mantissa                |
|  0   | 10000010 | 10001100000000000000000 |
```

Final result: $01000001010001100000000000000000_2$

## Integer Overflow and Integer Underflow

$N$-bit signed and unsigned integers have a certain range of values they may represent:

| Representation               | Range                             |
| ---------------------------- | --------------------------------- |
| Unsigned $N$-bit integer | $[0, 2^n − 1]$                |
| Signed $N$-bit integer   | $[−2^{n - 1}, 2^{n - 1} − 1]$ |

**Integer overflow** occurs when an arithmetic operation produces a result larger than the maximum value representable with the given number of bits. **Integer underflow** occurs when an arithmetic operation produces a result smaller than the minimum value representable with the given number of bits.

In Rust, integer overflow and underflow produces panics, while release builds produce the following behaviour:
- Unsigned integer overflows or underflows: the result will just be modded by $2^n$, that is, `(a op b) mod 2^n` (this also applies in general to any arithmetic on unsigned integers)
- Signed integers: the result will just be modded by $2^n$, but then, the value is interpreted according to two's complement representation.

## Integer Byte Order

**Byte order**, also known as **endianness** of a system defines how _multi $N$-byte_ chunks ($N \gt 1$) are assign to memory addresses.
- **Big endian Byte Order**: The most significant byte in the $N$-byte chunk is stored at the lowest memory address.
- **Little endian Byte Order**: The least significant byte in the $N$-byte chunk is stored at the lowest memory address.

A raw blob has no recoverable endianness on its own. Figuring it out requires an anchor: the scalar's size paired with a known value it should decode to.

> [!NOTE]
> On x86-64 systems (and most systems today), the byte ordering is _little endian_.

