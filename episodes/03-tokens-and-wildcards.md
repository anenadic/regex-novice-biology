---
title: Tokens and Wildcards
teaching: 0
exercises: 0
---

::: questions
- How can I specify that patterns should only match as whole words or whole lines?
- How can I only match patterns that appear at the very beginning or very end of a line?
- How can I specify positions in a pattern that could match any character?
:::

::: objectives
- Compose regular expressions that include tokens to match particular classes of character.
- Describe the risks associated with using tokens and wildcards that match many characters.
- Modify a regular expression to match only strings that appear at the start or end of a line or word.
:::

## Referencing multiple characters
In the introductory example we introduced the `\d` token, used to represent any single digit. In this regard, the two regular expressions below are equivalent.

```text
[0-9]
\d
```

### Tokens and the Backslash

Tokens in general are shorter form ways of describing standard, commonly-used character sets/classes. The table below describes the tokens available for use in regex.

Token | Matches                                                 | Set Equivalent |
------|---------------------------------------------------------|----------------|
`\d`  | Any digit                                               | `[0-9]`        |
`\s`  | Any whitespace character (space, tab, newlines)         | `[ \t\n\r\f]`  |
`\w`  | Any 'word character' - letters, numbers, and underscore | `[A-Za-z0-9_]` |
`\b`  | A word boundary character                               | no equivalent  |
`\D`  | The inverse of `\d` i.e. any character except a digit   | `[^0-9]`       |
`\S`  | Any non-whitespace character                            | `[^ \t\n\r\f]` |
`\W`  | Any non-word character                                  | `[^A-Za-z0-9_]`|

Notice that these tokens have a common syntax - a backslash character '\' followed by a letter establishing the set (lowercase) or inverse of a set (uppercase) being represented. The backslash is important in regular expressions, and in programming/command line computing in general. It is often used as an 'escape character' - it signifies to a program/interpreter that the character that follows the backslash should be treated in some special way. In regex, the backslash has a slightly confusing role, in that it is used as both an escape character and as a way of conferring special meaning on characters that would otherwise be treated literally. So, for tokens the inclusion of a backslash changes the meaning of the `w`, `s`, `b`, and `d` characters from "match exactly the character 'w'" and so on, to "match any character in this set/class". But for other characters that already have a special meaning (e.g. `.`, `$`, `[`, etc), the inclusion of a backslash in the preceding position in a regex tells the engine "treat this character literally, instead of interpreting it with a special value". We will discuss the implications of matching special characters in more detail in later sections.

This even extends so far as to the backslash character itself - you can specify that you want to match a literal backslash, by preceding that backslash character with - you guessed it! - a backslash i.e. with `\\`.

:::::: challenge

## Exercise 3.1

Match dates of the form 31.01.2017 (DAY.MONTH.YEAR) in the example file `person_info.csv`. Pay attention to not wrongly match phone numbers. How many matches do you find?

::: solution

## Solution

```text
\d\d\.\d\d\.\d\d\d\d
```
There are 25 matches in the file `person_info.csv` (every even record).

Note that the solution above will also match strings like 131.01.20171 or 99.99.9999.
If you really need to avoid matches like that,
you will need to construct a more specific regex,
e.g. one based on the ranges of years you expect to find in your date mateches.

:::
::::::

## Word Boundaries

The `\d`, `\w`, and `\s` tokens each represent a clearly defined set of characters. The `\b` token is more interesting - it is used to match what is referred to as a 'word boundary', and can be used to ensure matching of whole words only. For example, we want to find every instance of 'chr1' and 'chr2' in the file `example.gff`. Using what we've already learned, we can design the regex

```text
chr[12]
```

which will match either of the two target strings. However, this regex will also match all but the last character of 'chr13' and 'chr22', which is not what we want. How can we be sure that we will only match the two chromosome identifiers that we want, without additional digits on the end? We could add a space character to the end of the regex. But what if the target string appears at the end of a line? Or before a symbol/delimiter such as ';' or '.'? These strings will be missed by our regex ending with a space.

This is where the `\b` token comes in handy. 'Word boundary' characters include all of the options described above - symbols that might be used as field delimiters, periods and commas, newline characters, plus the special regex characters `^` and `$`, which refer to the beginning and end of a string respectively (more on these in a moment). So, by using the regex

```text
\bchr[12]\b
```

we ensure that we will only get matches to 'chr1' and 'chr2' as whole words, regardless of whether they are flanked by spaces, symbols, or the beginning or end of a line.

:::::: challenge

## Exercise 3.2
How can you refine the regex from the previous exercise to prevent matching strings with preceding or succeeding digits, such as 131.01.20171?

::: solution
## Solution

```text
\b\d\d\.\d\d\.\d\d\d\d\b
```

Note that this prevents matching strings like 131.01.20171, but still allows non-sensical dates such as 99.99.9999.
:::
::::::

:::::: challenge

## Exercise 3.3
When designing a regular expression to match exactly four digits, what would be the difference between using the two regular expressions `\b\d\d\d\d\b` and `\D\d\d\d\d\D`?

::: solution

## Solution

While both regular expressions will prevent leading and succeeding digits, the regular expression `\D\d\d\d\d\D` won't
work if the four digits appear at the beginning or end of the string. That is because the `\D` tokens MUST
match a character.
:::
::::::

## The `.` Wildcard

As well as the set tokens described above, there is also the more general wildcard `.`, which can be used to match any single character. Although it can be very helpful at times, it is recommended to use a more specific token/set wherever possible so as to avoid spurious matches - we will discuss more about this in the next section. Remember, to match a literal `.` character, escape it with a backslash i.e. `\.`.

## `^`Start and End`$`
The `^` and `$` symbols are used in a regex to represent the beginning and end of the searched line - they are refered to as 'anchor' characters. This can be extremely helpful when searching for lines that begin with a particular string/pattern, but where that pattern might also be found elsewhere in the lines.

In the example SAM file, 'example.sam', there are several header lines before the main body of individual alignments. These header lines begin with the '@' symbol, which is also contained within the quality strings and other fields of the alignment lines that make up the bulk of the file. If we search only with `@`, we won't be able to pull out only the header lines, so instead we can use the regex

```text
^@
```

to capture only the header lines. A similar approach can also be useful when searching for particular primer/adapter sequences in high-throughput DNA sequencing data.

:::::: challenge

## Exercise 3.4

Count how many sequences in `example_protein.fasta` are of transcript_biotype "protein_coding". Hint: sequence records have a header that starts with the character "`>`".

::: solution

## Solution

```text
^>.*transcript_biotype:protein_coding
```

There are 17 matches in the file `example_protein.fasta`.

_Note: be careful when using `>` in a regular expression on the command line - the `>` symbol has a special meaning in many command line environments, and using it can result in accidentally wiping the content of files etc._
:::
::::::


::: keypoints
- Use the `\b` token to match a word boundary, and `^` and `$` to match the beginning and end of a line respectively.
- `\\` has special meaning in regular expressions, and `\\\\` should be used to specify a literal backslash in a pattern.
- `.` describes a position that could match any character.
- When composing a regular expression, it is good practice to be as specific as possible about what you want to match.
:::
