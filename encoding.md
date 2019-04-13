
# Data
* We will use the following 4 terms as a starting point: __character__, __number__, __numeral system__, and __physical representation on disk__. These will serve as the "atomic units" of understanding how data is transformed on a computer.
    * __character__
        * A set of __characters__ is the representation of any given language's alphabet. The english alphabet has 26 characters for example.
    * __number__
        * A number seems pretty straightforward, but it's actually an abstract concept. What is the number for how many fingers you have? You could say it's `1010`, `10`, or `a` and all 3 would be accurate! Why? Because of __numeral systems__.
    * __numeral system__
        * This is peeling back a layer of abstraction on __numbers__. Turns out there are an arbitrary number of ways to represent a __number__! Getting kind of inceptiony here, but the concept of radix/base comes into play here. A radix of 10 (also known as base-10) is what we commonly know as decimal. There's also hexadecimal (base-16), binary (base-2), etc...
    * __physical representation on disk__
        * This is about how to represent data. Data is an abstract concept. If you peel back enough layers, all data on a computer system is represented using the binary numeral system. This is what we'll define as the __physical representation on disk__.
* __encoding__
    * Now let's define what we mean by the use of __encoding__ in this document. An encoding is the mapping of one of our atomic units to another atomic unit. So we can think of an encoding as a function: `f(atomic unit) -> atomic unit`.
    * However, there is an ordering to these atomic units. Let's explore this ordering and how it relates to __encoding__.
    * The term __encoding__ is used quite broadly in the software industry. We will attempt to disambiguate the term a bit and provide some more granular definitions of the types of __encodings__ out there and how they relate to our 4 atomic units.
* Unicode --- The set of all __characters__
    * Unicode is not an encoding. It is a __character set__ (a grouping of __characters__). It is the standardized way to represent ["every reasonable writing system on the planet."](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/) You can think of it as the world's largest alphabet. The Unicode __character set__ is represented using a concept known as code points. Every letter maps to a code point, which is represented something like this: `U+0041`. That is the english letter `A` (which has a different code point than `a`).
* Transforming a __character__ into a __number__
    * Now enter the different __character encodings__ (e.g., ASCII, ANSI, UTF-8, UTF-16, UTF-32, etc...). This is what we will call how we encode a __character__ into a __number__.
    * each character encoding has alphabets. when ascii doesn't understand something it uses that question mark box
    * ASCII and ANSI are old and can't represent Unicode. [UTF-8, UTF-16, and UTF-32](https://stackoverflow.com/questions/496321/utf-8-utf-16-and-utf-32) do however.
* Transforming a __number__ into a __numeral system__
    * Now enter the different __numeric encodings__ (e.g., Binary, Octal, Decimal, Hexadecimal, Base64, etc...). This is how we encode a __number__ into a __numeral system__ representation,
    * Any __character encoding__ from any of those aforementioned encodings can be represented by any of these __numeral systems__.
    * The UTF-8 encoding for [TETRAGRAM FOR STOVE](https://www.fileformat.info/info/unicode/char/1d331/index.htm) is `0xf09d8cb1` in hexadecimal, etc...
    * start with TETRAGRAM FOR STOVE and work your way through the flowchart
    * numeral system has alphabets
    * encoding requires a character encoding and a numeral encoding to be able to be represented? otherwise in a state of flux, it is a arbitrary concept. a character needs acsii, ansi, etc... to be represented as a number. a number needs binary, octal, etc... to be understood by a computer
* Final step: going from __numeral system__ to __physical representation on disk__
    * Remember __physical representation on disk__? It's the final state for our data. As we mentioned, it's binary (the binary __numeral system__ to be exact).
    * There is no "encoding" left here. We just convert whatever base we were in to binary and voila! If we were already in binary, then there was literally nothing to do.
* State ASCII art: __character__ -> __number__ -> __numeral system__ -> __physical representation on disk__
* 
* Base64
    * [Base64 vs. Base64URL](http://websecurityinfo.blogspot.com/2017/06/base64-encoding-vs-base64url-encoding.html)
        * URL encoding transforms a string into a valid URL.
        * Base64URL doesn't use `+` or `/` characters (instead using `-` and `_`) to make it URL and filesystem safe.
* [Padding](https://stackoverflow.com/questions/4080988/why-does-base64-encoding-require-padding-if-the-input-length-is-not-divisible-by)
    * Bit size
        * Base<sub>2</sub> has an alphabet size of 2, and therefore requires 1 bit to represent (i.e., `0` or `1`). 2<sup>1</sup> == 2.
            * 1 byte can hold 8 characters:
            ```
            1 byte / bit-size ---> 8 bits / bit-size ---> 8 / 1 ---> 8
            ```
        * Base<sub>16</sub> has an alphabet size of 16, and therefore requires 4 bits to represent (i.e., `0000`, `0001`, `0010`, ..., `1111`). 2<sup>4</sup> == 16.
            * 1 byte can hold 2 characters:
            ```
            1 byte / bit-size ---> 8 bits / bit-size ---> 8 / 4 ---> 2
            ```
        * Base<sub>64</sub> has an alphabet size of 64, and therefore requires 6 bits to represent. 2<sup>6</sup> == 64.
            * 1 byte can hold 1 character (but 3 bytes can hold 4 characters!):
            ```
            1 byte / bit-size ---> 8 bits / bit-size ---> 8 / 6 ---> 1.33
            3 bytes / bit-size ---> 24 bits / bit-size ---> 24 / 6 ---> 4
            ```
        * Base<sub>256</sub> has an alphabet size of 256, and therefore requires 8 bits to represent. 2<sup>8</sup> == 256.
            * 1 byte can hold 1 character:
            ```
            1 byte / bit-size ---> 8 bits / bit-size ---> 8 / 8 ---> 1
            ```

        * 2<sup>`bit-size`</sup> == `alphabet-size`
    * Base<sub>2</sub>, Base<sub>16</sub>, and Base<sub>256</sub> always fit evenly into a byte. Base<sub>64</sub>, however, does not.
    * Therefore it requires padding in order to make it 1 byte per character (thus maintaining data integrity):
        * `I` encodes to `SQ` (`SQ==` with padding)
        * `AM` encodes to `QU0` (`QU0=` with padding)
        * `TJM` encodes to `VEpN` (`VEpN` with padding)
        * > Concatenating and transmitting this data results in `SQQU0VEpN`. The receiver base64-decodes this as `I\x04\x14\xd1Q` instead of the intended `IAMTJM`. The result is nonsense because the sender has destroyed information about where each word ends in the encoded sequence. If the sender had sent `SQ==QU0=VEpN` instead, the receiver could have decoded this as three separate base64 sequences which would concatenate to give `IAMTJM`.


# blog
Let's start with the basics. What exactly is encoding? There are actually a few different ways the term "encoding" is used, which is part of the reason why the term can be so confusing. For now I'm going to focus on this purpose: to turn human language into something a computer can store. Okay, simple enough. So how exactly does that happen?

To explain the process I'm going to define a few terms to reduce ambiguity:
1. __character__
    * Characters are the symbols used to communicate. A letter can be a character, punctuation can be a character, etc... Even emojis can be considered characters.
1. __number__
    * A number seems pretty straightforward, but it's actually an abstract concept. What is the number for how many fingers you have? You could say it's `00001010`, `10`, or `a` and all three would be accurate!
1. __character encoding__
    * A character encoding is a mapping of __characters__ to __numbers__.
    * Examples include ASCII, ANSI, UTF-8, etc...
    * Unicode is _not_ a character encoding! More on this later.
1. __numeral system__
    * There are an arbitrary number of ways to represent a __number__. This is where is concept of radix, or as it's more commonly referred, base, comes into play.
    * A radix of 10 (also known as base-10) is what we refer to as decimal.
    * There's also hexadecimal (base-16), octal (base-8), binary (base-2), etc... `10` in decimal can be represented as `a` in hex, `12` in octal, and `00001010` in binary.
    * Base64 is _not_ a __numeral system__! Well technically base-64 is a valid __numeral system,__, but what is commonly referred to as Base64 is not. Base64 is actually a __character encoding__. It is similar to ASCII, but instead of holding 128 characters, Base64 only holds... 64 characters! I'll cover Base64 in more depth at the end.

I will be using two strings as example for how encoding works: `abc` and `abcŔŖ`. The first step in our encoding process is to convert a __character__ into a __number__ somehow. For that we will use a __character encoding__.

Let's start with `abc`. I created a text file on my computer via the command line (I'm using the Ubuntu subsystem on Windows 10 for this post). Here are some details:
```sh
$ cat abc.out
abc
```
```sh
$ file abc.out
abc.out: ASCII text
```
As you can see, Linux has done some determinations on its own and come to the conclusion that `abc.out` should ASCII encoding when being outputted to the screen.


----------------------
# What is encoding?
Have you ever come across some of these statements?
> This file is hex encoded

> This file uses an ASCII encoding

> This string is Unicode encoded

> Let's write the output to a UTF-8 encoded file

> Our message is safe because it's encoded using base64

These represent many of the ways the term "encode" is used across the industry. Frankly I found it all really confusing until I set out to write this post! I'm going to address each of these statements and attempt to define and disambiguate exactly what encoding means.

---
> ## This file is hex encoded
A similar phrase to hex encoding is binary encoding. Personally I don't like the use of the term "encoding" here. Technically an argument could be made that the semantics are correct. However I prefer using the term "representation". It makes encoding less of an overloaded definition and does a better job (in my mind at least) of describing what it actually is.

Hexadecimal (abbreviated as hex) and binary are both numeral systems. That's a fancy way of saying, "here's how to represent a number". If you step back and think about it, numbers are funny things. A number seems pretty straightforward, but it's actually an abstract concept. What is the number for how many fingers you have? You could say it's `00001010`, `10`, or `a` and all three would be accurate! We learn to say `10` because the easiest and most common numeral system for humans is decimal, also known as base-10. We have 10 fingers and 10 toes, so that makes learning how to count far more intuitive when we are infants.

If we instead applied that ease-of-use criteria to computers we would get binary (or base-2). Why? Because computers fundamentally think of things as being ["on" or "off"](https://www.howtogeek.com/367621/what-is-binary-and-why-do-computers-use-it/). Computers rely on electrons having either a positive charge or a negative charge to represent `1`s and `0`s. And it is with these `1`s and `0`s that the fundamentals of computers are accomplished, such as storing data and performing arithmetic calculations.

Great, so we can represent the same number in multiple ways. What use is that? Well let's refer back to the number ten. We could represent it in binary (`00001010`) or in hex (`a`). It takes eight characters in binary ( or four without the padding of `0`s), but only one in hex! That's due to the number of symbols each use. Binary uses two: `0` and `1`. Hex uses 16: `0`-`9` and `a`-`f`. The difference in representation size was stark enough for just the number ten, but it grows significantly more unequal when using larger numbers. So the advantage is that hex can represent large numbers much more efficiently than binary (and more efficiently than decimal too!).

Let's explore how to turn this theory into practical knowledge. To provide some examples for this post I created two files via the command line: `file1.txt` and `file2.txt`. Here are their contents outputted:
```
$ cat file1.txt
abc
```
```
$ cat file2.txt
abcŔŖ
```

Don't worry about the unfamiliar `R` characters at the end of `file2.txt`. I'll go over those details in-depth in the UTF-8 and Unicode sections. Now I will show the binary and hex representations of each file:
```bash
$ xxd -b file1.txt # binary
00000000: 01100001 01100010 01100011 00001010                    abc.
```
```bash
$ xxd file1.txt # hex
00000000: 6162 630a                                abc.
```
```bash
$ xxd -b file2.txt # binary
00000000: 01100001 01100010 01100011 11000101 10010100 11000101  abc...
00000006: 10010110 00001010                                      ..
```
```bash
$ xxd file2.txt # hex
00000000: 6162 63c5 94c5 960a                      abc.....
```

Again we see the compactness of hex on display. `file1.txt` requires 32 characters to represent in binary, but only 8 in hex. `file2.txt` requires 64 characters to represent in binary, but only 16 in hex. If we were to use a [hex to binary converter](https://www.mathsisfun.com/binary-decimal-hexadecimal-converter.html) we can see how these representations line up with one another.

Let's dissect `file1.txt`:
| Binary | Hexadecimal | Decimal |
| :---: |:---:| :---:|
| `01100001` | `61` | `97` |
| `01100010` | `62` | `98` |
| `01100011` | `63` | `99` |
| `00001010` | `0a` | `10` |

As mentioned above, binary is the numeral system that computers "understand". The binary representation of these two files are literally how these files are stored in the computer (what's known as bits, `1`s and `0`s, on the computer). The hex and decimal representation are just different ways of representing those bits. We can see that every byte in binary (1 byte is equal to 8 bits) lines up with 2 hex characters. And we can see what those same values would be if they were represented in decimal. But even armed with this understanding of hex and binary, there's still a lot to go. How does all this relate to the contents of `file1.txt`?

> ## This file uses an ASCII encoding

Remember that these binary, hex, and decimal representations are all of the same number. But we're not storing a number! We're storing `abc`. The problem is that computers have no concept of letters. They only understand numbers. So we need a way to say to the computer, "I want this character to translate to number X, this next character to translate to number Y, etc...". Enter ASCII.

Over the years ASCII has more or less become the defacto standard for encoding text written using the English alphabet. It assigns a numeric value for all 26 lowercase letters, all 26 uppercase letters, punctuation, symbols, and even the digits 0-9. Here is a picture of the ASCII table:

![](asciitable.jpg)

Here is the mapping of hex to ASCII using the ASCII table
| Hexadecimal | ASCII |
| :---: |:---:|
| `61` | `a` |
| `62` | `b` |
| `63` | `c` |
| `0a` | `LF` |

We can see `a`, `b`, and `c` there just as we would expect. What is that `LF` doing there at the end though? `LF` is a newline character in Unix (standing for "line feed"). However I didn't press the `Return` key when editing `file1.txt`. There should be no newline there! Actually, newlines are also used to indicate the end of a file (commonly abbreviated as `EOF`). Ubuntu inserted it for me, presumably because of how the [POSIX standard defines a line](https://stackoverflow.com/questions/729692/why-should-text-files-end-with-a-newline).

Great! This was an important step. We saw that the computer encodes the string `abc` into numbers in order to store it. We can then view the file as the computer has stored it in binary, or we can use different representations such as hex. `a` becomes `97`, `b` becomes `98`, `c` becomes `99`, and the Linux OS adds a `10` at the end to indicate `EOF`. ASCII is just a way to map numbers to characters.

If you would examine the ASCII table closely, you will see that it only maps to 128 characters. What do we do about characters from other languages? Other random symbols? Emojis???

> ## This string is Unicode encoded

As anglocentric as programming is, English is not the only language that needs to be supported. ASCII is fine for encoding English, but it is incapable of supprting anything else. This is where Unicode enters the fray. Unicode is not an encoding. [Wikipedia](https://en.wikipedia.org/wiki/Unicode) calls it a standard that can be implemented by different character encodings. I prefer to think of it as a giant alphabet. Unicode supports over [1.1 million characters](https://stackoverflow.com/questions/27415935/does-unicode-have-a-defined-maximum-number-of-code-points#27416004) in its alphabet. It does so through an abstraction called a code point. Every character has a [unique code point](https://unicode-table.com/en/). For example, `a` has a code point of `U+0061`. `b` has a code point of `U+0062`. And `c` has a code point of `U+0063`. Notice a pattern? `61` is the hex value for the character `a` in ASCII, and `U+0061` is the code point for `a` in Unicode. I'll come back to this point in the UTF-8 section.



## TL;DR
* Don't call hex and binary encodings. They are just different ways to represent the same number.
* ASCII and UTF-8 are encodings. They are the dictionaries that map bits the computer can understand into characters humans can understand.
* Unicode is not an encoding, it's an alphabet.
* Encoding `!=` encryption.
* Base64 is not a numeral system like hex or binary. It is similar to ASCII.