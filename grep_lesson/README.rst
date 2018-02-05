README's, Grep and Finding Things
====================================

*This lesson has been adapted from C.Titus Brown's Advanced Beginner/Intermediate Shell and Software Carpentry's Unix Lesson*

Learning goals:

* understand the importance of and use README files
* expose you to `grep` and `find`
* provide fodder for discussion, so please ask questions!

Points to make:

* I use almost everything below on a ~weekly basis.
* I learn new things every all the time ('set -e', for example).

-----

First, make sure you have and are in the correct directory. We will make a `work` directory on our Desktop and work within it::

   cd /Users/pliocene/Desktop
   mkdir work
   cd !$

NOTE: **!$** calls the last word of the previous command.

-----

Download and unpack test data::

   curl -O https://s3-us-west-1.amazonaws.com/dib-training.ucdavis.edu/shell-data.zip
   unzip shell-data.zip

and navigate to the `data/` directory.


----

Exploring directories with find
------------------------------

So, if we do 'ls', we see a bunch of stuff.  We didn't create this folder.
How do we figure out what's in it?

First, let's 'find' all of the directories::

   find . -type d

This walks systematically (recursively) through all files underneath '.',
finds all directories **(type d)**, and prints them (assumed, if not other
actions).

That's great but what if I want the only want to see the directories directly below the current directory::

   find * -prune -type d -print

We can easily see how large files are using ls -lah but what if we want to know the size of the directories?::

   find * -prune -type d -exec du -skh {} \;


Magic! Here we select directories with `-type d` and `exec` executes 'du' a command to display the disk usage statistic.

Let's use the find command to see if we have any fastq file::

   find *R1_001.fastq

Or what if we wanted to see all of the files::

   find . -type f

What other kind of "types" can we use?::

   b       block special
   c       character special
   d       directory
   f       regular file
   l       symbolic link
   p       FIFO
   s       socket


----

Grepping within files 
-------------------------

So now we know how to search for files or directories but what if we want to search within a file?
This is where the tool `grep` comes into play. GREP stands for **G**lobal **R**egular **E**xpression **P**rint.

We can search in one file with the setup::

   cd ../
   grep Hello hello.sh

What if we didn't know that Hello was capitalize––or if we didn't care?::

   grep -i "hElLo" hello.sh

We add the flag `-i` to **ignore cases**
Why does my terminal have color? You can set up colors in the .bash_profile in your $HOME directory.

Now let's get a bit more serious...navigate into the MiSeq directory::

  cd MiSeq

and take a look with `ls`. Again we see a lot of files.

What if we want to search a particular file for a sequence of interest?::

   grep "CGTTATCCGGATTTATT" F3D0_S188_L001_R1_001.fastq

Well, that's not too helpful! Can we get a little more out of 'grepping'?? Let's take a look at which lines have our sequence of interest.::

   grep -n "CGTTATCCGGATTTATT" F3D0_S188_L001_R1_001.fastq

Well that's still a pile of text––can we see how many lines contain this sequence?::

   grep -n "CGTTATCCGGATTTATT" F3D0_S188_L001_R1_001.fastq | wc -l

Compared to our original file with 1250 sequences that's quite a bit!

Okay, so now we know that our file contains 717 matches to our sequence of interest let's pull out all the information for each read and put it into a file.::

   grep -B 1 -A 2 "CGTTATCCGGATTTATT" F3D0_S188_L001_R1_001.fastq > matches.fastq

Here, the `-B` option captures the specified number of lines Before the line that matches and the `-A` option captures the number of line specified after.	

Or what if we want lines that DON'T match our sequence of interest?::

   grep -v -B 1 -A 2 "CGTTATCCGGATTTATT" F3D0_S188_L001_R1_001.fastq > matches.fastq

Here the `-v` option inverts our search and gives us all the lines that do not contain our search parameter.

We can also use regular expressions with `grep`::

   grep -E '^@' F3D0_S188_L001_R1_001.fastq > sequence.list

But we should always be skeptical of our commands... let's see how many sequences we have.::

   wc -l sequence.list

Hmm that's no quite right. Take a look inside with `less`. If we scroll down a bit we can see that we've accidently acquired lines with quality value. 
Perhaps we can adjust our search by refining the search::

  grep -E '^@M' F3D0_S188_L001_R1_001.fastq > sequence.list
   wc -l F3D0_S188_L001_R1_001.fastq

Seems about right.









----

Pipes and redirection:

To redirect stdin and stdout, you can use::

  > - send stdout to a file
  < - take stdin from a file
  | - take stdout from first command and make it stdin for second command
  >> - appends stdout to a previously-existing file

stderr (errors) can be redirected::

  2> - send stderr to a file

and you can also say::

  >& - to send all output to a file

Editing on the command line:

Most prompts support 'readline'-style editing.  This uses emacs control
keys.

Type something out; then type CTRL-a.  Now type CTRL-e.  Beginning and end!

Up arrows to recall previous command, left/right arrows, etc.

----

History tricks::

  !! - run previous command
  !-1 - run command-before-previous command (!-2 etc.)
  !$ - replace with the last word on the previous line
  !n - run the nth command in your 'history'


--------------------------

* break the task down into multiple commands
* put commands things in shell scripts, run in serial
* use intermediate i/o files to figure out what's going on!


Challenge exercise: how would you copy all files containing a specific string
('CGTTATCCGGATTTATTGGGTTTA', say) into a new directory? And what are the
pros (and cons) of your approach?


Other notes
-----------

Google (and especially stackoverflow) is your friend.
