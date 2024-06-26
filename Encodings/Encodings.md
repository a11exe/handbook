# Encodings

+ [ASCII](#ascii)
+ [UTF-8](#utf-8)

## ASCII

+ ASCII (/ˈæskiː/ ⓘ ASS-kee),[3]: 6 an acronym for American Standard Code for Information Interchange.
+ ASCII has just 128 code points, of which only 95 are printable characters.
+ Modern computer systems have evolved to use Unicode, which has millions of code points, but the first 128 of these are
  the same as the ASCII set.
+ Originally based on the (modern) English alphabet, ASCII encodes 128 specified characters into seven-bit integers
+ The reason the characters are encoded as 7-bit values is because early computers handled data in bytes — blocks of 8
  bits. The extra, eighth bit was originally reserved for error checking.

### Why 7 bit

+ Parity bits are used in digital communication and data storage to detect errors that may occur during transmission or
  storage. The purpose of a parity bit is to add some redundancy to the data being transmitted so that errors can be
  detected.
+ There are two main types of parity: even parity and odd parity. With even parity, the number of bits in a set (usually
  a byte) is set to have an even number of 1s. With odd parity, the number of bits in a set is set to have an odd number
  of 1s. The parity bit is then added to make the total number of 1s either even or odd, depending on the chosen parity
  type.
+ Parity bits are not exclusive to ASCII code; they can be used in any data transmission or storage system where error
  detection is important. While ASCII itself does not include parity bits, they can be added to ASCII-encoded data when
  it is being transmitted or stored to improve data integrity.
+ Parity bits are a simple and effective way to detect errors, but they do have limitations. They can only detect
  errors, not correct them

------------------------------------------------------------------------------
| Dec | Oct | Hex | Binary | Asc | Description |
------------------------------------------------------------------------------
| 0 | 0000 | 00 | 00000000 | NUL | Null char |
| 1 | 0001 | 01 | 00000001 | SOH | Start of Heading |
| 2 | 0002 | 02 | 00000010 | STX | Start of Text |
| 3 | 0003 | 03 | 00000011 | ETX | End of Text |
| 4 | 0004 | 04 | 00000100 | EOT | End of Transmission |
| 5 | 0005 | 05 | 00000101 | ENQ | Enquiry |
| 6 | 0006 | 06 | 00000110 | ACK | Acknowledgment |
| 7 | 0007 | 07 | 00000111 | BEL | Bell |
| 8 | 0010 | 08 | 00001000 | BS | Back Space |
| 9 | 0011 | 09 | 00001001 | HT | Horizontal Tab |
| 10 | 0012 | 0A | 00001010 | LF | Line Feed |
| 11 | 0013 | 0B | 00001011 | VT | Vertical Tab |
| 12 | 0014 | 0C | 00001100 | FF | Form Feed |
| 13 | 0015 | 0D | 00001101 | CR | Carriage Return |
| 14 | 0016 | 0E | 00001110 | SO | Shift Out / X-On |
| 15 | 0017 | 0F | 00001111 | SI | Shift In / X-Off |
| 16 | 0020 | 10 | 00010000 | DLE | Data Line Escape |
| 17 | 0021 | 11 | 00010001 | DC1 | Device Control 1 (oft. XON)            |
| 18 | 0022 | 12 | 00010010 | DC2 | Device Control 2 |
| 19 | 0023 | 13 | 00010011 | DC3 | Device Control 3 (oft. XOFF)           |
| 20 | 0024 | 14 | 00010100 | DC4 | Device Control 4 |
| 21 | 0025 | 15 | 00010101 | NAK | Negative Acknowledgement |
| 22 | 0026 | 16 | 00010110 | SYN | Synchronous Idle |
| 23 | 0027 | 17 | 00010111 | ETB | End of Transmit Block |
| 24 | 0030 | 18 | 00011000 | CAN | Cancel |
| 25 | 0031 | 19 | 00011001 | EM | End of Medium |
| 26 | 0032 | 1A | 00011010 | SUB | Substitute |
| 27 | 0033 | 1B | 00011011 | ESC | Escape |
| 28 | 0034 | 1C | 00011100 | FS | File Separator |
| 29 | 0035 | 1D | 00011101 | GS | Group Separator |
| 30 | 0036 | 1E | 00011110 | RS | Record Separator |
| 31 | 0037 | 1F | 00011111 | US | Unit Separator |
| 32 | 0040 | 20 | 00100000 | | Space |
| 33 | 0041 | 21 | 00100001 | !   | Exclamation mark |
| 34 | 0042 | 22 | 00100010 | "   | Double quotes (or speech marks)        |
| 35 | 0043 | 23 | 00100011 | # | Number |
| 36 | 0044 | 24 | 00100100 | $ | Dollar |
| 37 | 0045 | 25 | 00100101 | % | Procenttecken |
| 38 | 0046 | 26 | 00100110 | & | Ampersand |
| 39 | 0047 | 27 | 00100111 | '   | Single quote |
| 40 | 0050 | 28 | 00101000 | (   | Open parenthesis (or open bracket)     |
| 41 | 0051 | 29 | 00101001 | )   | Close parenthesis (or close bracket)   |
| 42 | 0052 | 2A | 00101010 | * | Asterisk |
| 43 | 0053 | 2B | 00101011 | + | Plus |
| 44 | 0054 | 2C | 00101100 | , | Comma |
| 45 | 0055 | 2D | 00101101 | - | Hyphen |
| 46 | 0056 | 2E | 00101110 | . | Period, dot or full stop |
| 47 | 0057 | 2F | 00101111 | / | Slash or divide |
| 48 | 0060 | 30 | 00110000 | 0 | Zero |
| 49 | 0061 | 31 | 00110001 | 1 | One |
| 50 | 0062 | 32 | 00110010 | 2 | Two |
| 51 | 0063 | 33 | 00110011 | 3 | Three |
| 52 | 0064 | 34 | 00110100 | 4 | Four |
| 53 | 0065 | 35 | 00110101 | 5 | Five |
| 54 | 0066 | 36 | 00110110 | 6 | Six |
| 55 | 0067 | 37 | 00110111 | 7 | Seven |
| 56 | 0070 | 38 | 00111000 | 8 | Eight |
| 57 | 0071 | 39 | 00111001 | 9 | Nine |
| 58 | 0072 | 3A | 00111010 | :   | Colon |
| 59 | 0073 | 3B | 00111011 | ; | Semicolon |
| 60 | 0074 | 3C | 00111100 | <   | Less than (or open angled bracket)     |
| 61 | 0075 | 3D | 00111101 | = | Equals |
| 62 | 0076 | 3E | 00111110 | >   | Greater than (or close angled bracket) |
| 63 | 0077 | 3F | 00111111 | ? | Question mark |
| 64 | 0100 | 40 | 01000000 | @ | At symbol |
| 65 | 0101 | 41 | 01000001 | A | Uppercase A |
| 66 | 0102 | 42 | 01000010 | B | Uppercase B |
| 67 | 0103 | 43 | 01000011 | C | Uppercase C |
| 68 | 0104 | 44 | 01000100 | D | Uppercase D |
| 69 | 0105 | 45 | 01000101 | E | Uppercase E |
| 70 | 0106 | 46 | 01000110 | F | Uppercase F |
| 71 | 0107 | 47 | 01000111 | G | Uppercase G |
| 72 | 0110 | 48 | 01001000 | H | Uppercase H |
| 73 | 0111 | 49 | 01001001 | I | Uppercase I |
| 74 | 0112 | 4A | 01001010 | J | Uppercase J |
| 75 | 0113 | 4B | 01001011 | K | Uppercase K |
| 76 | 0114 | 4C | 01001100 | L | Uppercase L |
| 77 | 0115 | 4D | 01001101 | M | Uppercase M |
| 78 | 0116 | 4E | 01001110 | N | Uppercase N |
| 79 | 0117 | 4F | 01001111 | O | Uppercase O |
| 80 | 0120 | 50 | 01010000 | P | Uppercase P |
| 81 | 0121 | 51 | 01010001 | Q | Uppercase Q |
| 82 | 0122 | 52 | 01010010 | R | Uppercase R |
| 83 | 0123 | 53 | 01010011 | S | Uppercase S |
| 84 | 0124 | 54 | 01010100 | T | Uppercase T |
| 85 | 0125 | 55 | 01010101 | U | Uppercase U |
| 86 | 0126 | 56 | 01010110 | V | Uppercase V |
| 87 | 0127 | 57 | 01010111 | W | Uppercase W |
| 88 | 0130 | 58 | 01011000 | X | Uppercase X |
| 89 | 0131 | 59 | 01011001 | Y | Uppercase Y |
| 90 | 0132 | 5A | 01011010 | Z | Uppercase Z |
| 91 | 0133 | 5B | 01011011 | [   | Opening bracket |
| 92 | 0134 | 5C | 01011100 | \ | Backslash |
| 93 | 0135 | 5D | 01011101 | ]   | Closing bracket |
| 94 | 0136 | 5E | 01011110 | ^ | Caret - circumflex |
| 95 | 0137 | 5F | 01011111 | _ | Underscore |
| 96 | 0140 | 60 | 01100000 | `   | Grave accent |
| 97 | 0141 | 61 | 01100001 | a | Lowercase a |
| 98 | 0142 | 62 | 01100010 | b | Lowercase b |
| 99 | 0143 | 63 | 01100011 | c | Lowercase c |
| 100 | 0144 | 64 | 01100100 | d | Lowercase d |
| 101 | 0145 | 65 | 01100101 | e | Lowercase e |
| 102 | 0146 | 66 | 01100110 | f | Lowercase f |
| 103 | 0147 | 67 | 01100111 | g | Lowercase g |
| 104 | 0150 | 68 | 01101000 | h | Lowercase h |
| 105 | 0151 | 69 | 01101001 | i | Lowercase i |
| 106 | 0152 | 6A | 01101010 | j | Lowercase j |
| 107 | 0153 | 6B | 01101011 | k | Lowercase k |
| 108 | 0154 | 6C | 01101100 | l | Lowercase l |
| 109 | 0155 | 6D | 01101101 | m | Lowercase m |
| 110 | 0156 | 6E | 01101110 | n | Lowercase n |
| 111 | 0157 | 6F | 01101111 | o | Lowercase o |
| 112 | 0160 | 70 | 01110000 | p | Lowercase p |
| 113 | 0161 | 71 | 01110001 | q | Lowercase q |
| 114 | 0162 | 72 | 01110010 | r | Lowercase r |
| 115 | 0163 | 73 | 01110011 | s | Lowercase s |
| 116 | 0164 | 74 | 01110100 | t | Lowercase t |
| 117 | 0165 | 75 | 01110101 | u | Lowercase u |
| 118 | 0166 | 76 | 01110110 | v | Lowercase v |
| 119 | 0167 | 77 | 01110111 | w | Lowercase w |
| 120 | 0170 | 78 | 01111000 | x | Lowercase x |
| 121 | 0171 | 79 | 01111001 | y | Lowercase y |
| 122 | 0172 | 7A | 01111010 | z | Lowercase z |
| 123 | 0173 | 7B | 01111011 | { | Opening brace |
| 124 | 0174 | 7C | 01111100 | | | Vertical bar |
| 125 | 0175 | 7D | 01111101 | } | Closing brace |
| 126 | 0176 | 7E | 01111110 | ~   | Equivalency sign - tilde |
| 127 | 0177 | 7F | 01111111 | DEL | Delete |
------------------------------------------------------------------------------

## UTF-8

The name is derived from Unicode Transformation Format – 8-bit.
UTF-8 is capable of encoding all 1,112,064[a] valid Unicode code points using one to four one-byte (8-bit) code units.
Code points with lower numerical values, which tend to occur more frequently, are encoded using fewer bytes.
It was designed for backward compatibility with ASCII: the first 128 characters of Unicode, which correspond one-to-one
with ASCII,
are encoded using a single byte with the same binary value as ASCII, so that valid ASCII text is valid UTF-8-encoded
Unicode as well.

UTF-8 encodes code points in one to four bytes, depending on the value of the code point.
In the following table, the x characters are replaced by the bits of the code point:

Code point ↔ UTF-8 conversion

| First code point | Last code point | Byte 1 | Byte 2 | Byte 3 | Byte 4 |
|---|---|---|---|---|---|
| U+0000 | U+007F | 0xxxxxxx | - | - | - |
| U+0080 | U+07FF | 110xxxxx | 10xxxxxx | - | - |
| U+0800 | U+FFFF | 1110xxxx | 10xxxxxx | 10xxxxxx | - |
| U+010000 | U+10FFFF | 11110xxx | 10xxxxxx | 10xxxxxx | 10xxxxxx |

The first 128 code points (ASCII) need 1 byte. The next 1,920 code points need two bytes to encode

+ The Unicode code point for the euro sign € is U+20AC.
+ As this code point lies between U+0800 and U+FFFF, this will take three bytes to encode.
+ Hexadecimal 20AC is binary 0010 0000 1010 1100. The two leading zeros are added because a three-byte encoding needs exactly sixteen bits from the code point.
+ Because the encoding will be three bytes long, its leading byte starts with three 1s, then a 0 (1110...)
+ The four most significant bits of the code point are stored in the remaining low order four bits of this byte (11100010), leaving 12 bits of the code point yet to be encoded (...0000 1010 1100).
+ All continuation bytes contain exactly six bits from the code point. So the next six bits of the code point are stored in the low order six bits of the next byte, and 10 is stored in the high order two bits to mark it as a continuation byte (so 10000010).
+ Finally the last six bits of the code point are stored in the low order six bits of the final byte, and again 10 is stored in the high order two bits (10101100).

1110**0010** 10**000010** 10**101100**