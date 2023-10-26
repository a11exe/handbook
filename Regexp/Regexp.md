# Regexp

Check regexp [https://regex101.com/](https://regex101.com/)

| Symbol    | Description                                                                                        |
|----------|----------------------------------------------------------------------------------------------------|
| . | It is called a wild card character, It matches any one character other than the new line.          |
| ^ | It matches the start of the string.                                                                |
| $ | It matches the end of the string.                                                                  |
| * | It matches up to zero or more occurrences i.e. any number of times of the character of the string. |
| \ | It is used for escape following character.                                                         |
|() | It is used to match or search for a set of regular expressions.                                    |
| ? | It matches exactly one character in the string or stream. ab? It will match either ‘a’ or ‘ab’                                         |

The traditional way to write a regular expression is by surrounding it with slashes.

```
/^he[l]{2}o\wworld$/
```

### Character shortcuts in regex

| Symbol | Description |
|----------|----------------------------------------------------------------------------------------------------|
| \c | Control character |
| \s | White space |
| \S | Not white space |
| \d | Digit |
| \D | Not digit |
| \w | Word |
| \W | Not word |
| \x | Hexadecimal digit |
| \O | Octal digit |
| [abc] | Any of a,b, or c |
| [^abc] | Not a, b, or c |
| [a-f] | Character from a to f |

0 or more -> `*`
1 or more -> `+`
0 or 1 -> `?`

```
/\w{3}/g # only 3 symbols
/\w{1,3}/g # from 1 to 3 symbols
/\w{0,3}/g # from 0 to 3 symbols
/\w{3,}/g # 3 or more symbols
```

```
/.+\.(doc|docx)/g # doc or docx
```

```
/fog/i # ignore case
/sa+/g # sa saa saaa
/sa*/g # s sa saaa
/sa?/g # s sa 
```

```
$ grep -P 'b?ar' # bar bear
$ grep -P '\-?[0-9]+\.[0-9]*' # 192.34 -12.2334
$ grep -P 'a(bc)?d' # abcd ad
$ grep -R root /etc # recursive search in etc all lines contains root
```

