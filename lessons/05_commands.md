
Six Glorious Commands

To continue advancing your Unix skills, we are going to learn some of the essentials of making more complicated commands. These are expanded here, but we are going to cover parts of Mike Lee’s “Six Glorious Commands”. Mike is a bioinformatician at NASA focused on microbial ecology, and if you haven’t seen the links on the resources page yet his website is a great resource!

Let’s start by downloading the files used in his tutorial, which I have put in the Command Line folder titled “Files for Interactive portion of Command Line Day 3” on BlackBoard. This will be a zipped file that you can upload to your VACC account and unzip by:

unzip six_commands.zip 

Take a look at some of the files. All of the files ending in tsv or txt are tab-delimited, while files ending in csv are delimited by columns.
Cut

The cut command performs the action of “Print selected parts of lines from each FILE to standard output” according to it’s manual. In practice this makes it an easy way to manipulate columns in many file types. The basic requirements of the command are some file that we want to manipulate and which columns we want.

Take a look at the gene_annotations.tsv file:

head gene_annotations.tsv

You should see four columns: gene_ID genome KO_ID KO_annotation

Using cut, we can pull out just the gene_ID column and print it to the screen. Instead of naming the columns we refer to their numerical order in the file:

cut -f 1 gene_annotations.tsv

Cut

Like other commands, we can use redirectors to modify how this information is displayed on our screen. For example we can look at just a few lines of this column by piping the output to head

cut -f 1 gene_annotations.tsv | head

Or to a completely new file with >

cut -f 1 gene_annotations.tsv > gene_ID.txt

Cut

You can also grab multiple columns at once, either by specifying all the individuals you want or supplying a range.

Specific columns

cut -f 1,3 gene_annotations.tsv | head

Column Range

cut -f 1-3 gene_annotations.tsv | head

Cut on other file types

We can use cut on a wide variety of files, but if the delimiter is anything but a tab we need to specify this as an additional parameter like so:

cut -d "," -f 1-3 example_gene_annotations.csv | head

grep

We’ve already seen a little bit of grep in action, but this tool has a wide range of potential uses. grep itself stands for global regular expression, which behaves like ctrl/command+f to search for strings of characters within a file. By default, searching this way will return any line that contains even a portion of the string you supply to it.

So when we look for “re” within our list of colors

grep re colors.txt

It will return both the lines “red” and “green”

Or you can search for specific, full names like we did on Day 1.
grep for annotation terms

Let’s say we’re interested in a specific annotation for the enzyme epoxyqueuosine reductase. There are two KO terms for this K09765 and K18979. Using grep we can quickly check for these in our data set:

grep K09765 gene_annotations.tsv

grep K18979 gene_annotations.tsv

Now imagine this was a very large data set, and these KO terms were really common. It wouldn’t be as user-friendly to just print all the lines to the screen, and instead we may want to just count the number of times they appear

grep -c K18979 gene_annotations.tsv

Can you make a command that would identify these rows, cut the gene_ID column, and then output this information into its own file called K18979_genes.txt?
paste

Just like it works on your local machine, paste combines columns horizontally (like cbind if you are familiar with R) with a given delimiter between them. The default is again tabs, but can be specified the same way as cut

There are two files with names of colors in this directory: colors.txt and colores.txt in Spanish. If you wanted to combine these two lists, you can paste them together (remember this only prints it to the screen and won’t actually create the new file!)

paste colors.txt colores.txt 

Now you can see the colors in English and Spanish, side by side as separate columns
paste

The files genes_and_seqs.tsv and gene_annotations.tsv contain the the sequences and annotations for the genes respectively. This is fairly common in bioinformatics, as if you didn’t need both sets of information the combined file would be needlessly large or visually messy. But we can combine these to make a file that contains all of this information using paste

paste gene_annotations.tsv genes_and_seqs.tsv | head -n 1

However, you can see that the gene_ID column is in there twice since it was included in both files? Can you use cut to show a new file that only has the first gene_ID column?
paste

Finally, paste itself doesn’t do any checking of your work. All it wants to do it join your files together, but doesn’t care if the rows are out of order or mismatched in any way. We will get to this more in the R lectures, but try checking the manuals for sort and comm. You can use sort to make sure both columns are in the same order and then check the output using comm
sed

sed stands for stream editor and is how you can “search and replace”. This is a very useful component of loops that you will learn about later, but is also really helpful for correcting typos, updating gene names with new nomenclature, and much more. We can use it here to change every instance of UW179A to UW277 in the gene_annotations.tsv file, which happens to be at the end of the file:

tail gene_annotations.tsv

You can see the change by again redirecting the changes we make with sed to tail

sed 's/UW179A/UW277/' gene_annotations.tsv | tail

The syntax of sed is a bit different from things that we’ve seen so far and consists of 4 arguments surrounded by ``: - s -> stands for substitution, telling sed we are taking one string and changing it to another - the string we want to find - the string we want to replace - a forth spot that is empty here, but we will see in a minute how we can use this to change the behavior of how many times in a file we change things

sed 's/UW179A/UW277/' gene_annotations.tsv | tail

Using sed globally

In the previous example, the string we wanted to replace only occurs in the genome column, but sometimes the string you want to search for occurs in multiple columns of a line known as occurrences.

A really common change to make is the editing of missing data or “NA”. Different programs will want missing data to be presented in various formats and it can be tedious to make these changes manually. If we want to change “NA” to “” for a specific program and we tried to do that using sed like the previous example, it would only catch the first occurrence of each row.

sed 's/NA/<NA>/' gene_annotations.tsv | head

To make changes to every time an “NA” appears, we need to tell sed that we want to apply the changes globally. This is where we use the forth slot, with a “g” for global

sed 's/NA/<NA>/g' gene_annotations.tsv | head

Does anyone notice the new problem we have created though?
sed

Since we were just searching for the string “NA”, we also changed any time it appeared in words like “DNA”. We can instead ask sed to match only full “words” instead like this:

sed 's/\<NA\>/<NA>/g' gene_annotations.tsv | head

This option is enabled by adding the characters “<” and “>” (remember when the links didn’t work on Day 1?), but because these are also part of the changes we want to make we add the "" so that the computer knows these aren’t normal characters that we want to search for!

sed also works with wild cards like * and ?, so you can imagine ways to make really powerful searches this way! But like everything else in Unix you have to be careful how you apply these changes. Starting with simple | and head like we have here can be a great way to check your work before you make permanent changes to a file.
awk

awk has even more uses than the other commands we just learned, but at its heart it is a command for filtering based on columns and making calculations from this information. We’re going to show a few uses with the blast_output.tsv file

head blast_output.tsv

We can see the 6 columns of a typical BLAST search: - query -> what we searched - qlen -> the length of the query - subject -> the reference sequence the query hit - slen -> the length of the subject - pident -> % identity of query to subject - al_length -> the length of the alignment or “overlap”

Oftentimes a BLAST search will give us a lot more hits than we actually want and we need to filter based on some kind of threshold. Let’s filter on hits that have at least 95% identity here.

You might be able to think of a really complicated way to combine grep, cut, and sed but we can do this all at once with a simple awk command:

awk ' $5 > 95 ' blast_output.tsv

awk takes the column we want to search in ($5) and then the filter we want to apply (> 95), all wrapped in ’’ so that it is treated as a single statement.
combining filters in awk

Much like the other commands, we can apply serval filters at once. These happen with “and/or” type statments: - and = && - or = ||

So now we could filter on >95% identity and query seqeunces that are at least 1,000 base pairs

awk ' $5 > 95 && $2 > 1000 ' blast_output.tsv

tr

Another useful command is tr which translates one character into another. It deals well with special characters, which occur a lot in genomics files but don’t play well with many GUI tools like Excel or when you need to read them into R packages. Conversely, even if you export things from Excel as a tab or comma separated file it may contain extra line breaks or special characters that will change how a Unix tools reads the files. Unix tools expect new lines to have \n, but Excel will sometimes use \r instead.

Take a look at the file gene_annotations_excel_exported.tsv

less gene_annotations_excel_exported.tsv

Instead of having nice line breaks, we can see ^M popping up all over our file. If we tried to count the lines, it would tell us there were 0!

wc -l gene_annotations_excel_exported.tsv

We can fix this using tr and avoid the frustrating problem of trying to fix our file in Excel.

Unlike the other commands we’ve seen, tr does not take an input file in a positional argument and needs it supplied via the redirector < (not to be confused with > that we’ve used so far). Can you think of a reason why it switches?
tr

Since we want to inform tr of input instead of output, we use < here

tr "\r" "\n" < gene_annotations_excel_exported.tsv > gene_annotations_fixed.tsv

If you head the new file you just made or count the lines, you should see the fix we made
Introduction to Loops

While wild cards can be used to perform simple actions on large numbers of files at once, they are limited in utility for more complex commands. In these instances we can turn to loops, a function of programming languages that allow us to write out a command and perform it on all of the samples or files that we want. In addition to being a major time saver, this makes your code a lot easier to read and much more concise overall.

There are many types of loops, but the kind we are going to cover is called a for loop. Before we get into creating our own we need to cover some of the key components.
Variables

The variable portion of a loop is the item that we are going to iterate over and will change with every “new” loop. This is typically each individual sample or file that the action is performed on. Outside of loops, you can set variables as shorthands, locations of specific files, or for many other reasons. In their simplest form they will look something like this:

var1=/long/path/to/DB

Setting this won’t print anything to your screen, but now the “value” (in this case a location to a specific database) is now assigned to the variable var1.

If we want to make sure this assignment worked, we can use an echo command. This is a command that prints out whatever is provided to it, which can be really useful to test commands or report information back to yourself during a loop.

For information stored in variables this needs to be preceded by a $ so the shell knows to evaluate what follows, rather than just treat it as generic characters.

echo $var1 

For loops

For loops are constructed with 4 basic words: - for -> set the loop variable name - in -> specify whatever it is we are looping over - do -> specify what we want to do with each item - done -> tell the computer we are done

A basic loop looks something like this when it’s written within a job script:

for i in A B C
do
  echo $i
done

But if you wanted to run a loop right from the command prompt itself we need to “indent” the different lines like so:

for i in A B C;do echo $i; done

The “i” is over variable, and as such could be any letter, word, etc. that was meaningful, but is commonly represented by a single letter like this for ease
For loops

For loops can also have multiple lines, performing multiple commands or actions:

for i in A B C
do
  echo $i
  echo $i >> words.txt
done

So now, not only are printing the output, but we are preserving it as a new file called words.txt
For loops

We in portion of the list will often be a directory instead of single files, such as your set of reads you want to work with. Let’s look at a more complex loop now:

for i in reads/*.fastq
do
  SAMPLE=$(echo ${i} | sed "s/.fastq//") 
  echo ${SAMPLE}.fastq
  
flye --nano-raw ${SAMPLE}.fastq --out-dir ${SAMPLE}_flye --plasmids --genome-size 2.5m --threads 16
  
done

This is an example of a for loop that could be used to run flye, a tool for genome assembly, on every file that ends in .fastq in the directory reads. Dissecting each line we see that:

    we’re setting the variable SAMPLE to be the characters that make up each sample name using sed to find and replace for the “word” .fastq with nothing. This let’s us have a variable that is essentially a list of every sample name.
    echo the names of each sample to make sure it’s correct
    perform the command itself. Now that the variable SAMPLE are the names without .fastq, we can use it for more than just the reads. This let’s us use that same variable to create output folders that are sampleName_flye

