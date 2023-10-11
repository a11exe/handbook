# Regexp

| Symbol    | Description                                                                                        |
|----------|----------------------------------------------------------------------------------------------------|
| . | It is called a wild card character, It matches any one character other than the new line.          |
| ^ | It matches the start of the string.                                                                |
| $ | It matches the end of the string.                                                                  |
| * | It matches up to zero or more occurrences i.e. any number of times of the character of the string. |
| \ | It is used for escape following character.                                                         |
|() | It is used to match or search for a set of regular expressions.                                    |
| ? | It matches exactly one character in the string or stream. ab? It will match either ‘a’ or ‘ab’                                         |

```
grep -P 'b?ar' // bar bear
grep -P '\-?[0-9]+\.[0-9]*' // 192.34 -12.2334
grep -P 'a(bc)?d' // abcd ad
```