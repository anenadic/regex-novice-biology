---
title: Alternative Matches
teaching: 0
exercises: 0
---

::::::::::::::::::::::::::::::::::::::: objectives

- Compose regular expressions to match several different strings.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- How can I define multiple possible strings that can be matched in a regular expression?

::::::::::::::::::::::::::::::::::::::::::::::::::

So far, we've seen how to perform very specific matching - literal string matching, using wildcards to match the same thing multiple times, etc - and how to match every substring that fits a particular pattern - e.g. all strings of at least six digits, every line starting or ending with a particular character, etc - but what if we only want to match under a limited number of set circumstances? For an example,consider again the FASTA file introduced in the previous chapter, a sample of which is reproduced below:

```text
>GX3597KLM "Homo sapiens"
MQSYASAMLSVFNSDDYSPAVQENIPALRRSSSFLCTESCNSKYQCETGENSKGNVQDRVKRPMNAFIVW
SRDQRRKMALENPRMRNSEISKQLGYQWKMLTEAEKWPFFQEAQKLQAMHREKYPNYKYRPRRKAKMLPK
NCSLLPADPASVLCSEVQLDNRLYRDDCTKATHSRMEHQLGHLPPINAASSPQQRDRYSHWTKL

>GK9113FGH "Homo sapiens"
MRPEGSLTYRVPERLRQGFCGVGRAAQALVCASAKEGTAFRMEAVQEGAAGVESEQAALGEEAVLLLDDI
MAEVEVVAEEEGLVERREEAQRAQQAVPGPGPMTPESAPEELLAVQVELEPVNAQARKAFSRQREKMERR
RKPHLDRRGAVIQSVPGFWANVIANHPQMSALITDEDEDMLSYMVSLEVGEEKHPVHLCKIMLFFRSNPY
FQNKVITKEYLVNITEYRASHSTPIEWYPDYEVEAYRRRHHNSSLNFFNWFSDHNFAGSNKIAEILCKDL
WRNPLQYYKRMKPPEEGTETSGDSQLLS

>GF7745PUP "Mus musculus"
MGRLVLLWGAAVFLLGGWMALGQGGAAEGVQIQIIYFNLETVQVTWNASKYSRTNLTFHYRFNGDEAYDQ
CTNYLLQEGHTSGCLLDAEQRDDILYFSIRNGTHPVFTASRWMVYYLKPSSPKHVRFSWHQDAVTVTCSD
LSYGDLLYEVQYRSPFDTEWQSKQENTCNVTIEGLDAEKCYSFWVRVKAMEDVYGPDTYPSDWSEVTCWQ
RGEIRDACAETPTPPKPKLSKFILISSLAILLMVSLLLLSLWKLWRVKKFLIPSVPDPKSIFPGLFEIHQ
GNFQEWITDTQNVAHLHKMAGAEQESGPEEPLVVQLAKTEAESPRMLDPQTEEKEASGGSLQLPHQPLQG
GDVVTIGGFTFVMNDRSYVAL

>GD8332BAG "Homo sapiens"
MEELTAFVSKSFDQKSKDGNGGGGGGGGKKDSITYREVLESGLARSRELGTSDSSLQDITEGGGHCPVHL
FKDHVDNDKEKLKEFGTARVAEGIYECKEKREDVKSEDEDGQTKLKQRRSRTNFTLEQLNELERLFDETH
YPDAFMREELSQRLGLSEARVQVWFQNRRAKCRKQENQMHKGVILGTANHLDACRVAPYVNMGALRMPFQ
QVQAQLQLEGVAHAHPHLHPHLAAHAPYLMFPPPPFGLPIASLAESASAAAVVAAAAKSNSKNSSIADLR
LKARKHAEALGL

>GK3091TFB "Mus musculus"
MAILFAVVARGTTILAKHAWCGGNFLEVTEQILAKIPSENNKLTYSHGNYLFHYICQDRIVYLCITDDDF
ERSRAFNFLNEIKKRFQTTYGSRAQTALPYAMNSEFSSVLAAQLKHHSENKGLDKVMETQAQVDELKGIM
VRNIDLVAQRGERLELLIDKTENLVDSSVTFKTTSRNLARAMCMKNLKLTIIIIIVSIVFIYIIVSPLCG
GFTWPSCVKK

>GK3141YRK "Pan troglodytes"
...

```

This FASTA file is *huge* - containing many tens of thousands of sequences. We would like to know how many sequences belonging to either *Pan troglodytes* **\*or\*** *Homo sapiens* are contained in the file. We could count the two species separately and add the results together, but what if we wanted the count for three species? Or ten? In this case, it might be better (and would almost certainly be faster) to perform a count using a set of optional matches in a single regular expression.

A set of options for matching in a regex can be defined using the `|`   pipe symbol. For example, to match either 'fish' or 'whale', we can construct the following expression:

```text
fish|whale
```

So, to match only 'Homo sapiens' or 'Pan troglodytes' in the FASTA file mentioned above, we can construct the regex:

```text
Homo sapiens|Pan troglodytes
```

which we can then use with `grep` or some other program to get a count of the matches. You can use this approach to selectively extract lines from a larger file, while preserving their order relative to each other. For example, this can be useful when subsetting a GFF annotation file based on feature type, source, etc.

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 6.1

If you study the contents of the file `person_info.csv`, you will see that some variation exists in the address formatting. For example, some of the addresses use 'First Street' while others use '1st Street' or some other variation.
Can you find all the lines containing information on a person living on 1st/First Street/street, using a single reglar expression?

::::::::::::::  solution

## Solution

Here are two possible solutions:

```text
[Ff]irst [Ss]treet|1st [Ss]treet
```

```text
(Fir|fir|1)st [Ss]treet
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 6.2a

The FASTQ file `example.fastq` contains sequence reads with quality scores, in the format

```text
@sequence header line with barcode sequence
sequence
+
quality scores for basecalls
```

Unfortunately, the barcode sequences in the header lines are wrong, and the barcodes are still attached to the front of the sequences. There are three barcodes that we are interested in: AGGCCA, TGTGAC, and GCTGAC.

How many reads are there in the file for each of these barcodes?

::::::::::::::  solution

## Solution

AGGCCA: 25

TGTGAC: 29

GCTGAC: 19
:::::::::::::::::

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 6.2b

Write a regular expression that will find and these barcodes and a replacement string that will remove from the start of the sequences in which they are found

::::::::::::::  solution

## Solution

regex: `^(AGGCCA|TGTGAC|GCTGAC)([ACGTN]+\n\+\n)`

replacement string: `\2` (`$2`)


:::::::::::::::::::::::::

::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 6.2c

Of course, the format of the file means that you should probably remove the quality scores associated with those sequence positions too. Rewrite your regex so that the barcode sequence AND its corresponding quality scores (i.e. the first six characters on the sequence and quality lines) are removed.

::::::::::::::  solution

## Solution

regex: `^(AGGCCA|TGTGAC|GCTGAC)([ACGTN]+\n\+\n).{6}`

replacement string: `\2` (`$2`)

::::::::::::::
:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::  challenge

## Exercise 6.2d

Finally, can you build on the regex and replacement string from part c), to replace the incorrect index sequences in the header lines with the barcodes for each relevant sequence record?

::::::::::::::  solution

## Solution

regex: `^(@VFI-SQ316.+:)GCGCTG\n(AGGCCA|TGTGAC|GCTGAC)([ACGTN]+\n\+\n).{6}`

replacement string: `\1\2\n\3`



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- Alternative strings to match can be combined with `|`.

::::::::::::::::::::::::::::::::::::::::::::::::::


