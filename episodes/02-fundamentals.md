---
title: Regex Fundamentals
teaching: 0
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Compose regular expressions to match patterns in text.
- Specify sets and ranges of characters to include in a search.
- Use inverted matches to exclude particular characters from a search.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I search for sets of characters in a text file?
- How can I specify ranges of characters in a search?

::::::::::::::::::::::::::::::::::::::::::::::::::

## Basic String Matching

In regular expression mode,
you can still search for a literal string
i.e. the exact letter(s)/word(s) that you want to find.
So, to find the gene name 'HDAC1', you would type:

```text
HDAC1
```

Using your text editor, open the example file `example.gff`
and try using the Find/Replace interface to search for this term.
Remember to make sure that you have your searches switched to regex mode.

Gene names are one example of where the letters in your target string
might be in upper or lower case (or a mix of the two) -
DNA and RNA sequences are another.
You should consider this when doing your search:
most search functions/programs provide an option to switch case
sensitivity on and off.
For example, when using `grep` on the command line,
the `-i` option activates case insensitivity.

```bash
grep -i HDAC1 example.gff
```

::::::::::::::::::::::::::::::::::::::  challenge

## Finding HDACs

Find every instance of a histone deacetylase (HDAC) in the file,
based on the gene name.
Are there any potential problems with your search?
Think about how you might make your search as specific as possible,
to avoid spurious matches.

::::::::::::::  solution

## Solution

To make the search less specific, remove the '1' from the end

```text
HDAC
```

Or, to be more specific and avoid any spurious matches:

```text
Name=HDAC
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Sets and Ranges

What if we want to find only 'HDAC1' and 'HDAC2' but no other patterns beginning with this gene name?
You could do this in two separate searches,
but getting everything you need in a single search might be better
for a number of reasons:
it is faster;
it will preserve the order of the results,
e.g. if you are extracting them to a separate file;
if you learn how to do it for two strings,
you can apply the same approach to ten, 100, etc;
and it is more satisfying!

In a regular expression,
we can specify groups of characters to be matched
in a certain position
by placing them between sqaure brackets `[]`.
For example,

```text
[bfr]oot
```

will match 'boot', 'foot', and 'root',
but not 'loot', or 'moot'.
Only a single character inside the square brackets `[]`
will be matched,
so the pattern above will not match the whole of
'froot' or 'broot' either.
As a substring of these, 'root' will be matched in both cases,
which may not be what we want.
We will learn more soon about how to avoid these partial matches.

This approach can be used to match only `HDAC1` and `HDAC2`
in our example GFF file, with the regex below:

```text
HDAC[12]
```

The set of characters specified inside `[]` can be a mix of letters, numbers, and symbols. So,

```text
[&k8Y]
```

is a valid set.

What if we want to match any uppercase letter? Applying what we've learned already, we *could* use the set

```text
[ABCDEFGHIJKLMNOPQRSTUVWXYZ]at
```

to match any string beginning with an upper case letter, followed by 'at' e.g. 'Cat', 'Bat', 'Mat', etc. But that's a lot of typing (30 characters to match only three in the string), and we can instead specify ranges of characters in `[]` with `-`. So, to match the same strings as before, our regex can instead look like this:

```text
[A-Z]at
```

Only seven characters now - that's much better! All lower case letters can be matched with the set `[a-z]`, and digits with `[0-9]`.

::::::::::::::::::::::::::::::::::::::::  callout

## Character Sets

Here, we will discuss only characters that fall inside the ASCII set - a limited set of roman alphabet letters, numbers, and symbols, which includes almost everything commonly used in English, but not accented characters (ü, é, ø, etc) or many specialised symbols (e.g. €, ¿, ±). Many regular expression engines provide a 'Unicode mode', often switched on with the 'u' flag, which will allow you to specify and match the full range of Unicode characters. This includes most alphabets, symbols, and even emojis.\_


::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 2.2a

In total, how many lines mention HDAC 1-5 in `example.gff`?

:::::::::::::::  solution

## Solution

82 (using the regex `Name=HDAC[1-5][^0-9]`)

:::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 2.2b

Which of the following expressions could you use to match any four-letter word beginning with an uppercase letter, followed by two vowels, and ending with 'd'?

```text
	i) [upper][vowel][vowel]d

	ii) [A-Z][a-u][a-u]d

	iii) [A-Z][aeiou][aeiou]d

	iv) [A-Z][aeiou]*2;d
```

::: solution

## Solution

Option iii) fits the description.
You might also have chosen option ii),
which would match the described pattern,
but also other non-vowel letters in the middle two positions.

:::
::::::


::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 2.2c

Try playing around with the character ranges that you have learned above. What does `[A-z]` evaluate to? What about `[a-Z]`? Can you create a range that covers all letter and number characters?

::: solution

## Solution

`[A-z]` matches all letter characters (both upper and lower case).
`[a-Z]` is an invalid set.
`[A-9]` will match any letter or digit character.

:::
::::::::::::::::::::::::::::::::::::::::::::::::::

Ranges don't have to include the whole alphabet or every digit - we can match only the second half of the alphabet with

```text
[N-Z]
```

and only the numbers from 5 to 8 with

```text
[5-8]
```

We can specify multiple ranges together in the same set, so matching 'a', 'b', 'c', 'd', 'e', 'f', or any digit can be done with

```text
[a-f0-9]
```

But if we're using the `-` symbol to specify a range of characters, how do we include the literal '-' symbol in a set to be matched? To do this, the `-` should be specified at the start of the set. So

```text
[-A-K]
```

will match '-' as well as any uppercase letter between 'A' and 'K'.

## Inverted sets

The last thing to tell you about sets and ranges (for now), is that we can also specify a set or range of characters to *not* match in a position. This is achieved with the `^` symbol at the beginning of the set.

```text
201[^269]
```

will match '2010', '201K', '201j', etc, but not '2012', '2016', or '2019'. In contrast to `-`, which is only taken literally when at the start of the set, `^` only takes a special meaning at the start of a set - it is treated literally if it appears anywhere else in the set. If you want to invert a set that should include the `-` symbol, start the set with `^-` followed by whatever other characters you don't want to match.

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 2.3

Use an inverted set to only match the human autosomes (chr1-22), i.e. filtering out chromosomes chrX, chrY and chrM. How many records with autosomes can you find in file `example.gff`?

::::::::::::::  solution

## Solution

```text
chr[^XYM]
```

There are 897 records matching this regular expression.
In fact, there are only 895 lines beginning with `chr[^XYZ]`,
but two other lines also match the regex above because they contain the string 'chromosome'.
To avoid matching these, anchor the regex to the beginning of the line with `^`
i.e. `^chr[^MXY]` (see chapter 3).

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Wrap characters in `[]` to define a set of valid matches for a given position.
- Use `-` between two characters to define a range of characters to match.
- `^` at the start of a set to invert it, indicating that the given characters should be excluded from a match.

::::::::::::::::::::::::::::::::::::::::::::::::::


