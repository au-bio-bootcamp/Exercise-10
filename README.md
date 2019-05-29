# Small Group Exercise #10:
EMBOSS and Scripting

In this exercise you are going to take multiple nucleotide sequence data files in GenBank format, reformat them, translate to amino acid sequence data, determine codon usage and look for a specific protein motif.  To do this you are going to blend together tools from EMBOSS with your working knowledge of shell commands and scripting. In this example, you will need to track files and file extensions on your own. First we will start with a single file.

1. Explore the documentation EMBOSS at the SourceForge website and note the wide range of tools”
* http://emboss.sourceforge.net
* http://emboss.sourceforge.net/apps/groups.html

2. Reformat a Genbank file.

3.  Make a new directory in your HOME called `emboss` and move the file `/home/shared/biobootcamp/data/Lamellibrachia_luymesi_MITOS_data/Lamellibrachia_luymesi_mtDNA_genome.gb` into it. Look at the context of this file. This is GenBank format where the features are annotated but the file is in a format that is not easy to manipulate. You want to **pull out the codon features (CDS) from this file**.
    1.  EMBOSS has a program for helping find utilities within the EMBOSS software suite. We want a program that will help use extract features from the Genbank file. Type `wossname` and try the keyword `feature` when prompted. Does one of these utilities look useful for extracting features?
    2. Try `extractfeat –h`. Use this command to extract the codon features from the Genbank file. Use the flags `-type CDS –join`. You will have to sort out the input and output flags.
    3. Check your output file and verify it is in fasta format and verify the sequences are only CDs (hint: look at headers). The rename the file with the proper extension (.fasta) if needed. You can use the `mv` or `rename` command - syntax: `rename current_pattern desired_pattern file`
    4. Next modify the fasta headers (text after ‘>’) of these files to `Lamellibrachia_luymesi_CDS`. Which command allows substitutions? Use cheat sheet and/or Google if needed to figure out format of the regular expression.

4. Translate nucleotide data to amino acid data
    1. Use `wossname` to find an EMBOSS translation tool.
    2. Pick the appropriate tool and translate using the correct syntax.
    3. Open the file and check the translation. Any stop codons (indicated by \*)?
    4.  If there are stop codons, check translation table flag (-table). You want to use the invertebrate translation table.
    5. Fortunately, our data is in frame but that is not always the case. Try the three following EMBOSS commands:
        ```bash
        sixpack –sequence <INFILE> -table 5 -outseq <OUTFILE>.sixpack.seqs -outfile <OUTFILE>.sixpack.out
        showorf -sequence <INFILE> -frames 1,2,3 -table 5 -outfile <OUTFILE>.showorf
        showorf -sequence <INFILE> -frames 1,2,3,4,5,6 -table 5 -outfile <OUTFILE>.showorf
        ```

5.  Create a codon usage table from nucleotide sequence. Start with the wossname command to find the correct EMBOSS utility.


6. Now that you have the protein sequence. You may be interested in finding a particular protein domain to help identify the protein family, PFAM. Try the `patmatdb` command with the motif “PEWY”. You can learn about this motif at http://www.ncbi.nlm.nih.gov/pmc/articles/PMC4122944/.

****TIME TO AUTOMATE – read through steps 7-15 before starting work****
 Most of this week you have been doing one or two commands at a time. However, power of scripting is that you can make the computer do a lot of work for you by a script to work through a set of files. One of the most common ways to do this is a for-do-done loop.  You are going to write a script to automate many of the steps above.


7. Copy the directory `/home/shared/biobootcamp/data/Tubeworm_mtDNAs/` to your HOME directory. Look at these files. What are they?  You are going to write a script that will perform the following on each file:
    1.  Extract the CDS features into a fasta formatted file with the correct extension
    2. Change the fasta headers to include the name of the file employed (which includes the species name).
    3. Translate the sequences to AA sequences.
    4. Create a codon usage table from nucleotide sequence each file.
    5. Print results of searching for the motif `PEWY` for each file.


You can do this – you just have to “put it all together”. Stop and think about each of these steps. What commands will you need?  Look above. You are encouraged to outline the script on scrap paper.

8. You will make your script in `nano`. Look at the top lines of any .sh script you have used this week. Have you noticed they all start the same way? What does this mean? Also notice that next two lines about modules often look similar. Do you need any modules here? Did you use any above? Call the script what you want but end it with `.sh`
    1. Go ahead and write the top of the script.
    2. The # allows you to write comments in the file that are not noticed during execution. Use the # symbol to add a comment at the top of your script that includes important information: who wrote the script, when, and what it does.

9. Instead of thinking about trying to conduct all these commands on all the files at once. Let’s focus on working on one file at a time and then repeating that process on the other files. We can do this easily with a for-do-done loop. As discussed the general format is:

```bash
for FILE in FILENAME
  do
    COMMAND $FILE
  done
```

This approach will grab the file name and stick in a variable called FILE. You can call that variable with `$FILE`.  FILENAME refers to the files you want to do the commands on. If you use `*.fasta` for FILENAME, this loop will work, in sequential order on all `.fasta` files in the directory from which the script is executed. Remember what the `*` means in REGREX.  Write the for-do loop  above into your script.

10. Look at the files you moved in (7). Is there a common feature to all these files that you can use to describe the FILENAME?

11. Now change COMMAND (in FOR-DO loop in #9) to be the first command(s) you want to execute (see 7a). For this first command, $FILE will be the input file. The name for your output file should include a variable so that you do not overwrite the output file each time you run the loop.  Your first command should look something like this:
    `extractfeat -sequence $FILE.gb -type CDS -join -outseq $FILE.fasta`

12.  OPTION: you can either try and write the complete script and then execute it on all the files, or you can add one or a few steps and test it on a one or two files at a time before running the entire thing. Which do you think if the wiser way to go? That is correct! Make a directory for testing with one or two of the files ending in .gb (from step 7) and test your script in there. Remember to `chmod` before executing. You can execute at the prompt. You do NOT need to submit these to the queue.

13. Add the commands from steps 7.ii, 7.iii, 7.iv, and 7.v as needed. This will all go be for you reach “done”. For these commands, remember that the output for 7a will be the input for 7b. Also the output for 7b will be the input for 7c, etc. To help with this, use your file extensions for output files in a smart way.

14. Test your script. Once it is working move it to the folder with all 6 Tubeworm GenBank files and execute.


15. Optional. If you want to be fancier, you can use the echo command help view the progress of the script. For example, just before the done line try:
      `echo $FILE “ has been processed!”`


REAL WORLD EXAMPLE

16. Copy the file `/home/shared/biobootcamp/data/first400.fastq` to your directory and look at it. This file is the first 400 lines of an Ion Torrent fastq file. How does it look different from the Illumina fastq files you have seen? Although the reads are different lengths, we know that all the full length reads (about 220bp) end in one of several known barcodes. We are interested in pulling out all the sequences that have the barcode “TCTAGA”, trimming the barcode, and writing these sequence and associated fastq lines to a file called `TCTAGA_reads.fastq`.

All of this can be accomplished in one line with two commands and a redirect. Think about how you can use what you know about the barcode, its position, and the format of a fastq file. Write the command and see if it works.

Describe how might you automate this process to place sequences with different barcodes in separate files?
