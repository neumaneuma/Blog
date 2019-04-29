# What is an encoding?
Have you ever come across some of these statements?
> This file is hex encoded

> This file uses an ASCII encoding

> This string is Unicode encoded

> Let's write the output to a UTF-8 encoded file

> Our message is safe because it's encoded using Base64

> Python uses Unicode strings for encoding

These represent many of the ways the term "encode" is used across the industry. Frankly I found it all really confusing until I set out to write this post! I'm going to address each of these statements and attempt to define and disambiguate exactly what encoding means.

---
> ## This file is hex encoded
A similar phrase to hex encoding is binary encoding. Personally I don't like the use of the term "encoding" here. Technically an argument could be made that the semantics are correct. However I prefer using the term "representation". It makes encoding less of an overloaded definition. Also, "representation" does a better job (in my mind at least) of describing what is actually happening.

Hexadecimal (abbreviated as hex) and binary are both numeral systems. That's a fancy way of saying, "here's how to represent a number". If you step back and think about it, numbers are funny things. A number seems pretty straightforward, but it's actually an abstract concept. What is the number for how many fingers you have? You could say it's `00001010`, `10`, or `a` and all three would be accurate! We learn to say `10` because the easiest and most common numeral system for humans is decimal, also known as base-10. We have 10 fingers and 10 toes, so that makes learning how to count far more intuitive when we are infants.

If we instead applied that ease-of-use criteria to computers we would get binary (or base-2). Why? Because computers fundamentally think of things as being ["on" or "off"](https://www.howtogeek.com/367621/what-is-binary-and-why-do-computers-use-it/). Computers rely on electrons having either a positive charge or a negative charge to represent `1`s and `0`s. And it is with these `1`s and `0`s that the fundamentals of computing are accomplished, such as storing data or performing mathematical calculations.

Great, so we can represent the same number in multiple ways. What use is that? Let's refer back to the number ten. We could represent it in binary (`00001010`) or in hex (`a`). It takes eight characters in binary (or four without the padding of `0`s), but only one in hex! That's due to the number of symbols each use. Binary uses two: `0` and `1`. Hex uses 16: `0`-`9` and `a`-`f`. The difference in representation size was stark enough for just the number ten, but it grows significantly more unequal when using larger numbers. So the advantage is that hex can represent large numbers much more efficiently than binary (and more efficiently than decimal too for that matter).

Let's explore how to turn this theory into practical knowledge. To provide some examples for this post I created two files via the command line: `file1.txt` and `file2.txt`. Here are their contents outputted:
```bash
$ cat file1.txt
abc
```
```bash
$ cat file2.txt
abcŔŖ
```

Don't worry about the unfamiliar `R` characters at the end of `file2.txt`. I'll go over those details in-depth in the UTF-8 and Unicode sections. For now I will just show the binary and hex representations of each file:
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
| :---: | :---: | :---: |
| `01100001` | `61` | `97` |
| `01100010` | `62` | `98` |
| `01100011` | `63` | `99` |
| `00001010` | `0a` | `10` |

As mentioned above, binary is the numeral system that computers "understand". The binary representation of these two files are literally how these files are stored in the computer (as what are known as bits, `1`s and `0`s, on the computer). The hex and decimal representations are just different ways of representing those bits. We can see that every byte in binary (1 byte is equal to 8 bits) lines up with 2 hex characters. And we can see what those same values would be if they were represented in decimal. For reference, the largest 1 byte binary value is `11111111`, which is `ff` in hex and `255` in decimal. The smallest 1 byte binary value is `00000000`, which is `00` in hex and `0` in decimal. But even armed with this understanding of hex and binary, there's still a lot to go. How does all this relate to the contents of `file1.txt`?

> ## This file uses an ASCII encoding

Remember that these binary, hex, and decimal representations are all of the same number. But we're not storing a number! We're storing `abc`. The problem is that computers have no concept of letters. They only understand numbers. So we need a way to say to the computer, "I want this character to translate to number X, this next character to translate to number Y, etc...". Enter ASCII.

Back in the day, ASCII was more or less the de facto standard for encoding text written using the English alphabet. It assigns a numeric value for all 26 lowercase letters, all 26 uppercase letters, punctuation, symbols, and even the digits 0-9. Here is a picture of the ASCII table:

![](asciitable.jpg)

Here is the mapping of `file1.txt`'s hex values to their ASCII characters using the ASCII table:

| Hexadecimal | ASCII |
| :---: | :---: |
| `61` | `a` |
| `62` | `b` |
| `63` | `c` |
| `0a` | `LF` |

We can see `a`, `b`, and `c` there just as we would expect. What is that `LF` doing there at the end though? `LF` is a newline character in Unix (standing for "line feed"). I pressed the `Return` key when editing `file1.txt`, so that added a newline.

Any character in the ASCII character set requires only 1 byte to store. ASCII supports 128 characters, as we saw in the ASCII table. However, 1 byte allows for 256 (or 2<sup>8</sup>) values to be represented. In decimal that would be `0` (`00000000` in binary) through `255` (`11111111` in binary). That should mean ASCII can support 128 more characters. Why isn't that the case? ASCII only required 128 characters to support English text and its accompanying symbols so presumably that was all that was taken into account when the ASCII standard was formalized. As a result, ASCII only uses 7 of the 8 bits in a byte. However, that leads to a lot of waste -- half of the values are unused! 128 additional characters could be supported.

[Joel Spolsky](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/) wrote an excellent blog post on this problem. Basically the issue was fragmentation. Everyone agreed what the first 128 values should map to, but then everyone went and decided their own usage for the remaining 128 values. As a result there was no consistency among different locales.

Let's review what we learned so far. We saw that the computer encodes the string `abc` into bits in order to store it. We can then view these bits as the computer has stored it in binary, or we can use different representations such as hex. `a` becomes `97`, `b` becomes `98`, `c` becomes `99`, and the Linux OS adds a `10` at the end to indicate `EOF`. ASCII is just a way to map bits (that computers understand) to characters (that humans understand).

ASCII leaves a gaping issue though. There are a lot more than 128 characters in use! What do we do about characters from other languages? Other random symbols? Emojis???

> ## This string is Unicode encoded

As anglocentric as programming is in 2019, English is not the only language that needs to be supported on the web. ASCII is fine for encoding English, but it is incapable of supporting anything else. This is where Unicode enters the fray. Unicode is not an encoding. That point bears repeating. Unicode is _not_ an encoding.

[Wikipedia](https://en.wikipedia.org/wiki/Unicode) calls it a standard that can be implemented by different character encodings. I find that definition, while succinct, too abstract. Instead, I prefer to think of it like this:

> Imagine you have a giant alphabet. It can support over 1 million characters. It is a superset of every language known to humankind. It can support made-up languages. It contains every bizarre symbol you can think of. It has emojis. And all that only fills about 15% of its character set. There is space for so much more to be added. It's impractical to have a keyboard that has button combinations for over 1 million different characters. The keyboard I'm using right now has 47 buttons dedicated to typeable characters. With the `Shift` key that number is doubled. That's nowhere close to 1 million though. There needs to be some way to use the characters in this alphabet!

> In order to make this alphabet usable we're going to put it in a giant dictionary.  A normal dictionary would map words to their respective definitions. In this special dictionary we'll have numbers mapping to all these characters. So to type the character you want, you will type the number for it. And then it will be someone else's job to replace those numbers with the characters they map to in the dictionary. Just as the words are in alphabetical order, the numbers will be in ascending order. And for the characters not yet filled in, we'll just have a blank entry next to the unused numbers.

This is Unicode in a nutshell. It's a dictionary that supports an alphabet of over [1.1 million characters](https://stackoverflow.com/questions/27415935/does-unicode-have-a-defined-maximum-number-of-code-points#27416004). It does so through an abstraction called a code point. Every character has a [unique code point](https://unicode-table.com/en/). For example, `a` has a code point of `U+0061`. `b` has a code point of `U+0062`. And `c` has a code point of `U+0063`. Notice a pattern? `61` is the hex value for the character `a` in ASCII, and `U+0061` is the code point for `a` in Unicode. I'll come back to this point in the UTF-8 section.

The structure of a code point is as follows: `U+` followed by a hex string. The smallest that hex string could be is `0000` and the largest is `10FFFF`. So `U+0000` is the smallest code point (representing the `Null` character) and `U+10FFFF` is the largest code point (currently unassigned). As of [Unicode 12.0.0](http://www.unicode.org/versions/Unicode12.0.0/) there are almost 138,000 code points in use, meaning slightly under 1 million remain. I think it's safe to say we won't be running out anytime soon.

ASCII can map bits on a computer to the English alphabet, but it wouldn't know what to do with Unicode. So we need a character encoding that can map bits on a computer to Unicode code points (which in turn maps to a giant alphabet). This is where UTF-8 comes into play.

> ## Let's write the output to a UTF-8 encoded file
UTF-8 is one of several encodings that support Unicode. You may have heard of some of the others: UTF-16 LE, UTF-16 BE, UTF-32, UCS-2, UTF-7, etc... I'm going to ignore all the rest of these though. Why? Because UTF-8 is by far the dominant encoding of the group. It is backwards compatible with ASCII, and according to [Wikipedia](https://en.wikipedia.org/wiki/UTF-8), it accounts for over 90% of all web page encodings.

UTF-8 uses different byte sizes depending on what code point is being referenced. This is the feature that allows it to maintain backwards compatibility with ASCII.

![](utf8.JPG)

<sup>Source: Wikipedia</sup>

If UTF-8 encounters a byte that starts with `0`, it knows it found a starting byte and that the character is only one byte in length. If UTF-8 encounters a byte that starts with `110` then it knows it found a starting byte and to look for two bytes in total. For three bytes it is `1110`, and four bytes it is `11110`. All continuation bytes (i.e., the non-starting bytes; bytes 2, 3, or 4) will start with a `10`. The [reason for these continuation bytes](https://www.quora.com/Why-do-subsequent-bytes-in-UTF-8-need-to-start-with-10-when-the-first-byte-already-contains-the-information-on-how-many-bytes-in-total-are-used) is that it allows you to be able to find the starting byte of a character easily.

As a refresher, this is what `file2.txt` looks like on the command line:
```bash
$ cat file2.txt
abcŔŖ
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

Let's dissect `file2.txt` to understand how UTF-8 works:

| Hexadecimal | UTF-8 | Unicode Code Point |
| :---: | :---: | :---: |
| `61` | `a` | `U+0061` |
| `62` | `b` | `U+0062` |
| `63` | `c` | `U+0063` |
| `c594` | `Ŕ` | `U+0154` |
| `c596` | `Ŗ` | `U+0156` |
| `0a` | `LF` | `U+000A` |


We can see that the hex representations for `a`, `b`, `c`, and `LF` are the same as for `file1.txt`, and that they align perfectly with their respective code points. The hex representations for `Ŕ` and `Ŗ` are twice as long as the other hex representations though. This means that they require 2 bytes to store instead of 1 byte.

Here is a table showing the different representations and the type of byte side-by-side:

| Byte type | Binary | Hexadecimal | Decimal | UTF-8 |
| :---: | :---: | :---: | :---: | :---: |
| Starting Byte | `01100001` | `61` | `97` | `a` |
| Starting Byte | `01100010` | `62` | `98` | `b` |
| Starting Byte | `01100011` | `63` | `99` | `c` |
| Starting Byte | `11000101` | `c5` | `197` | `Ŕ` |
| Continuation Byte | `10010100` | `94` | `148` | `Ŕ` (contd.) |
| Starting Byte | `11000101` | `c5` | `197` | `Ŗ` |
| Continuation Byte | `10010110` | `96` | `150` | `Ŗ` (contd.) |
| Starting Byte | `00001010` | `0a` | `10` | `LF` |


UTF-8 uses 1 byte to encode ASCII characters, and multiple bytes to encode non-ASCII characters. To be precise it uses 7 bits to encode ASCII characters, exactly like ASCII does. Every byte on disk that maps to an ASCII character will map to the exact same character in UTF-8. And any other code point outside of that range will just use additional bytes to be encoded.

As I alluded to earlier, the code points for `a`, `b`, and `c` match up exactly with the hex representations of those letters in ASCII. I suppose that the designers of Unicode did this in the hopes that it would make backwards compatibility with ASCII easier. UTF-8 made full use of this. Its first 128 characters require one byte to encode. Despite having room for 128 more characters in its first byte, UTF-8 instead required its 129th character to use 2 bytes. [`DEL`](https://unicode-table.com/en/007F/) is the 128th character (#127 on the page because the table starts at 0) and has the hex representation `7F`, totalling 1 byte. [`XXX`](https://unicode-table.com/en/0080/) (no, not the character for porn) is the 129th character and has the hex representation `C280`, totalling 2 bytes.

If you're curious here are examples of characters requiring over 2 bytes:
* 3 bytes: [`㚈`](https://unicode-table.com/en/3688/)
* 4 bytes: [`🜁`](https://unicode-table.com/en/1F701/)

Just to re-emphasize what is happening here: UTF-8 maps bytes on disk to a code point. That code point maps to a character in Unicode. A different encoding, like UTF-32 for example, would map those same bytes to a completely different code point. Or perhaps it wouldn't even have a mapping from those bytes to a valid code point. The point is that a series of bytes could be interpreted in totally different ways depending on the encoding.

> ## Our message is safe because it's encoded using Base64

This statement deals with several different concepts. I'll start by going over the different types of encoding.

As best as I can tell there are 2 different types of encodings: [character encodings](https://en.wikipedia.org/wiki/Character_encoding) and [binary-to-text encodings](https://en.wikipedia.org/wiki/Binary-to-text_encoding). ASCII and UTF-8 are examples of character encodings. Base64 is one example of a binary-to-text encoding.

What's the difference? Both character encodings and binary-to-text encodings share the same goal of turning bits into characters. However, character encodings are designed to produce human-readable output. Binary-to-text encodings are designed to turn bits into human-printable output.

Wait, what? That was a nebulous distinction you say? Okay, let me try to explain it in a different way. A character encoding like ASCII is really good for data storage and transmission. For example, say you're writing a speech. You want to save it on your computer so you don't have to re-type it every time. The computer stores that speech as a bunch of `1`s and `0`s. ASCII is needed to translate those bits back into the words, letters, and punctuation that make up the speech. In the same way, say you want to upload the speech to the cloud. The exact same process is needed to transport that speech over the Internet.

Base64 is an example of a binary-to-text encoding. In fact, it's pretty much the only one in use, much like UTF-8 is for character encodings. It is a subset of ASCII, containing 64 of the 128 ASCII characters: `a-z`, `A-Z`, `0-9`, `+`, and `/`. It doesn't contain characters like `NUL` or `EOF`. Those characters are non-printable characters. Base64 is often used to translate a binary file to text, or even a text file with non-printable characters to one with only printable characters. The benefits of this are that you can output the contents of any type of file, no matter what data it contains. It doesn't have to be limited to a file either; it can be just a string, such as a password. Also, you are guaranteed to always have characters that can be displayed, no matter what the underlying bits are. That is something UTF-8 cannot accomplish. How does Base64 do it?

I described in the UTF-8 section how certain bit patterns at the start of a byte indicate how many bytes the character will be. `0` for 1 byte, `110` for 2 bytes, `1110` for 3 bytes, and `11110` for 4 bytes. And it uses `10` to indicate a byte is a continuation byte. This means that byte sequences that don't follow this pattern are incomprehensible to UTF-8. For example, UTF-8 doesn't understand `11111111`. Let's show this on the command line with a new file, `file3.txt`:

```bash
$ cat fil3.txt
123
```
```bash
$ xxd -b file3.txt
00000000: 00110001 00110010 00110011 00001010                    123.
```
```bash
$ printf '\xff' | dd of=file3.txt bs=1 seek=0 count=1 conv=notrunc # overwrite the first byte with 11111111
1+0 records in
1+0 records out
1 byte copied, 0.0009188 s, 1.1 kB/s
```
```bash
$ xxd -b file3.txt
00000000: 11111111 00110010 00110011 00001010                    .23.
```
This is what the file looked like in VSCode using a UTF-8 encoding before being overwritten with the `printf '\xff' | dd...` command:

![](beforeOverwrite.jpg)

And this is what it looked like after:

![](afterOverwrite.jpg)

As mentioned before, Base64 can always display printable characters, even when UTF-8 cannot. Let's see that in action:

```bash
$ base64 file3.txt > file4.txt
```

And now the file has printable characters:

![](b64.jpg)

Base64 has 64 characters in its alphabet. That means it only needs 6 bits to represent the whole alphabet (2<sup>6</sup> == 64). Instead of the UTF-8 approach of using the leading bits in a byte as metadata and the remaining bits to store the actual data, Base64 uses the entire byte as data. It has no metadata. However, as I mentioned it only uses 6 bits. A byte has 8 bits. How does this math line up?



First things first, encoding is not the same as encryption. I guess people confuse the terms because they both start with "enc", and both take plaintext and turn it into gibberish. 

Encoding turns plaintext into seeming gibberish, however, it is intended to be easily turned back into plaintext. 

> ## Python uses Unicode strings for encoding

In python 2 there are a class of string literals that are known as [unicode strings](https://docs.python.org/2/howto/unicode.html#encodings). They are delineated by prefixing the character `u` to a string literal (e.g., `u'abc'`). I am not a fan of the term unicode string because it leads to the confusion that unicode is an encoding. So what exactly does python mean when it refers to unicode strings?

Let's look at some examples in Python 2.7.12:
```python
>>> a = u'abc'
>>> b = u'abcŔŖ'
>>> a
u'abc'
>>> b
u'abc\u0154\u0156'
```
So we define 2 strings, `a` and `b`, which contain the same contents as `file1.txt` and `file2.txt` did. `a` is able to be printed out to the console without an issue, but the console can't render `ŔŖ` at the end of `b`. Instead those characters are replaced with their unicode code points: `\u0154` (`U+0154`) and `\u0156` (`U+0156`). It appears that the python 2 interpreter can only print strings using ASCII, and not a unicode-compatible encoding.

Let's try explicitly encoding these strings:
```python
>>> a.encode('utf-8')
'abc'
>>> a.encode('ascii')
'abc'
>>> b.encode('utf-8')
'abc\xc5\x94\xc5\x96'
>>> b.encode('ascii')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'ascii' codec can't encode characters in position 3-4: ordinal not in range(128)
```
String `a` can be encoded using both ASCII and UTF-8 as expected. Also as expected, encoding string `b` using ASCII results in an error since neither `Ŕ` nor `Ŗ` are ASCII compatible. And encoding string `b` using UTF-8 renders a string that is a mix of ASCII characters (what python 2 can handle) and the hex representations of the non-ASCII characters python 2 couldn't handle.

A unicode string in python 2 is just a combination of ASCII-compatible characters and the code points of non-ASCII compatible characters. What about python 3? Python 3 got rid of the distinction between a regular string (e.g., `abc`) and a unicode string (e.g., `u'abc'`), and just has regular strings without any prefixes. Does this mean there are no unicode strings in python 3?

Let's find out using Python 3.5.2:
```python
>>> a = "abc"
>>> b = 'abcŔŖ'
>>> a
'abc'
>>> b
'abcŔŖ'
```

Python 3 treats every string as a unicode string, and on top of that, can print non-ASCII compatible characters to the console now. Also the `encode()` function still works the same:

```python
>>> b.encode('utf-8')
b'abc\xc5\x94\xc5\x96'
```

The only other question remaining is how to print out the code points?
```python
>>> b.encode('unicode_escape')
b'abc\\u0154\\u0156'
```



## TL;DR
* Don't call hex and binary encodings. They are just different ways to represent the same number.
* ASCII and UTF-8 are encodings. They are the dictionaries that map bits the computer can understand into characters humans can understand.
* Unicode is not an encoding. Technically it is a standard, but I like to think about it as a dictionary for a giant alphabet.
* Encoding `!=` encryption.
* Base64 is not a numeral system like hex or binary. It is an encoding similar to ASCII.
