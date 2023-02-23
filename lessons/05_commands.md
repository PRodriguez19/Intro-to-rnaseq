---
Week: "6" 
Lesson: "Bash continued"
Date: "Thursday, February 23, 2023"
---

# More Bash Commands

To continue advancing your UNIX skills, we are going to learn some of the essentials of making more complicated commands. These are expanded here, but we are going to cover parts of [Mike Lee’s “Six Glorious Commands”](https://astrobiomike.github.io/unix/six-glorious-commands). Mike is a bioinformatician at NASA focused on microbial ecology and if you have not seen the links on the resources page yet -- his website is a great resource!

Let’s start by downloading the files used in his tutorial, which can be found in the shared drive: 

```
cp -r /gpfs1/cl/mmg232/course_materials/six_commands/ .
```

Take a look at some of the files. All of the files ending in .tsv or .txt are tab-delimited, while files ending in .csv are delimited by columns.

## cut

The cut command performs the action of “Print selected parts of lines from each FILE to standard output” according to it’s manual. In practice this makes it an easy way to manipulate columns in many file types. The basic requirements of the command are some file that we want to manipulate and which columns we want.

Take a look at the gene_annotations.tsv file:

```bash
head gene_annotations.tsv
```

You should see four columns: gene_ID genome KO_ID KO_annotation

```
gene_ID	genome	KO_ID	KO_annotation
1	CC9311	K02338	DPO3B; DNA polymerase III subunit beta [EC:2.7.7.7]
2	CC9311	NA	NA
3	CC9311	K01952	purL; phosphoribosylformylglycinamidine synthase [EC:6.3.5.3]
4	CC9311	K00764	purF; amidophosphoribosyltransferase [EC:2.4.2.14]
5	CC9311	K02469	gyrA; DNA gyrase subunit A [EC:5.99.1.3]
6	CC9311	NA	NA
7	CC9311	K18979	queG; epoxyqueuosine reductase [EC:1.17.99.6]
8	CC9311	NA	NA
9	CC9311	NA	NA
```

Using cut, we can pull out just the gene_ID column and print it to the screen. Instead of naming the columns we refer to their numerical order in the file:

```bash
cut -f 1 gene_annotations.tsv
```

We can also use redirectors to modify how this information is displayed on our screen. For example we can look at just a few lines of this column by piping (|) the output to head

```bash
cut -f 1 gene_annotations.tsv | head
```

Or to a completely new file with >

```bash
cut -f 1 gene_annotations.tsv > gene_ID.txt
```

You can also grab multiple columns at once, either by specifying all the individuals you want or supplying a range.

```bash
cut -f 1,3 gene_annotations.tsv | head
```

Column Range

```bash
cut -f 1-3 gene_annotations.tsv | head
```

### Using cut on other file types

We can use cut on a wide variety of files, but if the delimiter is anything but a tab we need to specify this as an additional parameter like so:

```bash
cut -d "," -f 1-3 example_gene_annotations.csv | head
```

## grep

grep has a wide range of potential uses. Grep itself stands for *global regular expression*, which behaves like ctrl/command+f to search for strings of characters within a file. By default, searching this way will return any line that contains even a portion of the string you supply to it.

So, lets look at the file colors.txt 

```bash
head colors.txt
```

Let's look for “re” within our list of colors using grep

```bash
grep re colors.txt
```

It will return both the lines “red” and “green”

Or you can search for specific, full names like we did previously.

## paste

paste will combine columns horizontally (like cbind if you are familiar with R) with a given delimiter between them. The default delimiter is `tab`, but this can be specified 

There are two files with names of colors in this directory: colors.txt and colores.txt in Spanish. If you wanted to combine these two lists, you can paste them together (remember this only prints it to the screen and won’t actually create the new file!)

```bash
paste colors.txt colores.txt 
```

Now you can see the colors in English and Spanish, side by side as separate columns

```
red	rojo
orange	anaranjado
yellow	amarillo
green	verde
blue	azul
indigo	indigo
violet	violeta
```

The files `genes_and_seqs.tsv` and `gene_annotations.tsv` contain the the sequences and annotations for these genes, respectively. 

```
head -n 3 genes_and_seqs.tsv

gene_ID	AA_length	seq
1	385	MKLVCSQAELNAALQLVSRAVASRPTHPVLANVLLTADAGTDRLSLTGFDLNLGIQTSLAASVDTSGAVTLPARLLGEIVSKLSSDSPVSLSSDAGADQVELTSSSGSYQMRGMPADDFPELPLVENGTALRVDPASLLKALRATLFASSGDEAKQLLTGVHLRFNQKRLEAASTDGHRLAMLTVEDALQAEISAEESEPDELAVTLPARSLREVERLMASWKGGDPVSLFCERGQVVVLAADQMVTSRTLEGTYPNYRQLIPDGFSRTIDLDRRAFISALERIAVLADQHNNVVRIATEPATGLVQISADAQDVGSGSESLPAEINGDAVQIAFNARYVLDGLKAMDCDRVRLSCNAPTTPAILTPANDDPGLTYLVMPVQIRT
2	237	MAWMHPPVHRLLGWVSRPSALRTSRDVWRLDQCRGFDDQQVFVKGAPAEADQITLDRLPTLLDADLLNADGERVGIIADLAFLPASGQISHYLVARSDPRLPGTSRWRLLPDRIVDQQPGLVSSAIHELDDLPLARASVRQDFLQRSRHWREQLQQFGDRAGERLEGWLEEPPWDEPPAVSDVASSYSSTAAPTVDPLDDWDDGDWTDAPRVERGRSVRNDPTDRNDWPDHEEDPWV
```

```
head -n 3 gene_annotations.tsv

gene_ID	genome	KO_ID	KO_annotation
1	CC9311	K02338	DPO3B; DNA polymerase III subunit beta [EC:2.7.7.7]
2	CC9311	NA	NA
```

We can combine these to make a file that contains all of this information using paste: 

```
paste gene_annotations.tsv genes_and_seqs.tsv | head -n 1
```

Notice that the gene_ID column is in there twice since it was included in both files. These can be cut out in the new file! 


> Paste itself does not check your work. All it wants to do it join your files together, but it does not care if the rows are out of order or mismatched in any way. You can use `sort` to make sure both columns are in the same order and then check the output

## sed

sed stands for stream editor and is used as an “search and replace”. This is a very useful component of for loops and for correcting typos, updating gene names with new nomenclature, and much more. 

We can use it here to change every instance of UW179A to UW277 in the gene_annotations.tsv file, which happens to be at the end of the file:

```bash
tail gene_annotations.tsv

91	UW179A	NA	NA
92	UW179A	NA	NA
93	UW179A	NA	NA
94	UW179A	K06181	rluE; 23S rRNA pseudouridine2457 synthase [EC:5.4.99.20]
95	UW179A	NA	NA
96	UW179A	K02518	infA; translation initiation factor IF-1
97	UW179A	K00384	trxB; thioredoxin reductase (NADPH) [EC:1.8.1.9]
98	UW179A	NA	NA
99	UW179A	NA	NA
100	UW179A	NA	NA
```

You can see the change by again redirecting the changes we make with sed to tail

```bash
sed 's/UW179A/UW277/' gene_annotations.tsv | tail
```
> Notice, that the syntax of sed is a bit different from things that we’ve seen so far and consists of 4 items that are separated by 3 forward slashes. The 1st item is a letter "s" stands for substitute; the 2nd is what we'd like to find and replace; the 3rd is what we'd like to replace it with, and the 4th is actually empty here but we will see how we can change this in a minute. 


### Using sed globally

In the previous example, the string we wanted to replace only occurs in the genome column (2nd column in file), but sometimes the string you want to search for occurs in multiple columns in a single line! 

A really common change to make in the bioinformatics world is editing missing data or the occurrence of “NA”. Different programs will want missing data to be presented in various formats and it can be tedious to make these changes manually. 

Let's first look at our file again

```bash
head gene_annotations.tsv
```
> Notice that NA appears in both KO_ID (3rd column) and the KO_annotation (4th) column 

Using the same format as before, lets change the "NA" to "<NA>" 

```bash
sed 's/NA/<NA>/' gene_annotations.tsv | head
```
> Notice that this only changed the first occurrence of "NA" - 3rd column only! 

To make changes for every time an “NA” appears, we need to tell sed that we want to apply the changes globally. This is where we use the forth slot comes into play, with a “g” for global

```bash
sed 's/NA/<NA>/g' gene_annotations.tsv | head
```

Does anyone notice the new problem we have created though?

```
gene_ID	genome	KO_ID	KO_annotation
1	CC9311	K02338	DPO3B; D<NA> polymerase III subunit beta [EC:2.7.7.7]
2	CC9311	<NA>	NA
3	CC9311	K01952	purL; phosphoribosylformylglycinamidine synthase [EC:6.3.5.3]
```
> Since we were just searching for the string “NA”, we also changed any time it appeared in words like “DNA”. 

We can instead ask sed to match only full “words” like this:

```
sed 's/\<NA\>/<NA>/g' gene_annotations.tsv | head
```
> We need to surround our pattern with “<” and “>”, but we need to escape slash them so that it doesn’t treat them as the normal characters and look for them.

On a MacOS it would look like below! 
```
sed "s/[[:<:]]NA[[:>:]]/<NA>/g" gene_annotations.tsv | head
```

## awk

awk has even more uses than the other commands we just learned, but at its heart it is a command for filtering based on columns and making calculations from this information. We’re going to show a few uses with the blast_output.tsv file

```bash
head blast_output.tsv
```

```
query   qlen    subject slen    pident  al_length
Te_3773 505     3R_1113 505     100.0   505
Te_3773 505     8R_1720 342     96.3    241
Te_4114 726     3R_1358 1473    88.7    726
Te_4114 726     8R_523  445     89.7    395
Te_4114 726     8R_524  958     89.3    371
Te_4133 1470    3R_1088 8618    96.8    1470
Te_4133 1470    3R_1087 8642    100.0   200
Te_4133 1470    8R_4656 1973    89.4    1470
Te_4136 891     3R_1088 8618    97.6    891
```

We can see the 6 columns of a typical BLAST search: - query -> what we searched - qlen -> the length of the query - subject -> the reference sequence the query hit - slen -> the length of the subject - pident -> % identity of query to subject - al_length -> the length of the alignment or “overlap”

Oftentimes a BLAST search will give us a lot more hits than we actually want and we need to filter based on some kind of threshold. Let’s filter on hits that have at least 95% identity here.

You might be able to think of a really complicated way to combine grep, cut, and sed but we can do this all at once with a simple awk command:

```bash
awk ' $5 > 95 ' blast_output.tsv
```
> awk takes the column we want to search in ($5) and then the filter we want to apply (> 95), all wrapped in ’’ so that it is treated as a single statement.

### combining filters in awk

Much like the other commands, we can apply serval filters at once. These happen with “and/or” type statements. An "and" is specified with `&&` while "or" is specified with two pipes `||`. 

So now we could filter on >95% identity (pident = column 5) and query seqeunces (qlen = column 2) that are at least 1,000 base pairs

```bash 
awk ' $5 > 95 && $2 > 1000 ' blast_output.tsv
```


