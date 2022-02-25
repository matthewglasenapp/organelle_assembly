We will be doing our organellar genome assemblies on UCSC's Hummingbird computational cluster. A cluster, or supercomputer, is a group of computers that work together and function as a single system. The advantage of using Hummingbird is that it has much more memory and storage than your own personal computers. We can submit jobs to run on hummingbird and will be notified by email when they complete. We do not have to keep our computer running or monitor the progress. We have create a group folder on hummingbird for our class. The group folder is located at `/hb/groups/bioe137/`.

We interact with Hummingbird via the command line interface using UNIX commands. UNIX is an operating system that includes a collection of built-in tools and commands.

To log in to hummingbird, first open the terminal application (Mac users) or Cygwin (Windows users). We will use the `ssh` command to access Hummingbird. `ssh` stands for secure shell and provides a secure connection between your computer and the Hummingbird server. To login, you will use the following command:  
  
  `ssh <your_cruzid>@hb.ucsc.edu`  
  
  Replace `<your_cruzid>` with your UCSC username in your command. You will be prompted to enter your password. For security measures, you will not be able to see the characters you are entering. Type your password and press enter.
  
  The following message should appear on your screen. ![alt text](https://user-images.githubusercontent.com/60276545/155772584-312a9baf-f2bc-484d-8254-ddf65895da40.png)   
  
  Congrats! You are now logged into hummingbird. When you log in to Hummingbird, you are automatically taken to your home directory. To see the path of your working directory, use the `pwd` command. `pwd` stands for "print working directory." When you type `pwd` and hit enter, `/hb/home/<your_cruzid>` will be printed to the terminal. This is your personal directory, where you can store files and data.  
  
  For this class and activity, we will be working in a different directory. We have created a group folder located at `/hb/groups/bioe137/`. Navigate to this directory using the `cd` command. cd stands for "change directory." To change directories, you type `cd` followed by the path to the directory you would like to change to. Type the following command:  
  
  `cd /hb/groups/bioe137/` 
  
  Let's confirm that we navigated to the correct directory by using the `pwd` command again. When you type `pwd` and hit enter, you should see `/hb/groups/bioe137`. Let's see what is in this directory. Type `ls` and hit enter. ls stands for "list" and lists all of the files and directories in the current working directory. Directories (folders) are printed in blue font and files are printed in white font. You will see that we have created a directory for each student. There is also a directory called NOVOPlasty. We have installed the organelle genome assembly software for you.  
  
  Navigate to your directory using the `cd` command. Type `cd <your_last_name>` and hit enter. Let's see what is in your directory using the `ls` command. You should see two files. These are your raw sequencing reads in fastq format! The .gz extension indicates that they have been compressed using gzip. This means that they will not be in human-readable format. This is fine because it saves storage space and our assembly software can handle compressed files. 
  
  Now, lets check out the directory that contains the NOVOPlasty assembly software. Enter the following commands:  
  
  `cd /hb/groups/bioe137/NOVOPlasty/`  
  `ls`  
  
  Check out the contents of this directory. On Hummingbird, executables are printed in green. NOVOPlasty4.3.1.pl is the executable. The .pl extension indicates that this software was written in the perl programming language. Notice that there is a README file. This is common practice for software packages. Let's check out this file! There are several options for viewing files on the command line interace. We will begin with the `less` command, which allows us to view the contents of the file. Type the following command and press enter:
  
  `less README.md`
  
  You can navigate the contents of the file by using either your trackpad or the arrow keys on your keyboard. This file contains the instrutions for running the NOVOPlasty software. To exit out of this view, simply hit the q key, which stands for quit. 
  
  Let's try one more option for viewing and editing text files. nano is a built-in text editor on UNIX operating systems. Type the following command and press enter:
  
  `nano README.md`
  
  Again, you can navigate the contents of the file by using either your trackpad or the arrow keys on your keyboard. However, `nano` allows us to actually modify the text or add new text. We won't change anything to this file. You would not normally edit the text of a README file that someone else has written. To exit `nano`, hit Ctrl+X.
  
  Now, let's look at the config.txt file. This is the configuration file that provides instructions to the software program.   
  
  `less config.txt`
  
  The top half of the file includes paramters that you have to set yourself by entering text. The bottom half of the file provides instructions for how to fill in information for each field. Each of you will need this configuration file in your own directories. Let's make a copy of this file and move it to our own directories using the `cp` command, which stands for copy. 
  
  `cp config.txt /hb/groups/bioe137/<your_last_name>/`
  
  Let's confirm that we copied the file to the correct directory by navigating to that directory. 
  
  `cd /hb/groups/bioe137<your_last_name>/`
  
  `ls`
  
  You should now see config.txt in your personal directory. 
  
  One of the fields, "Seed Input," asks us for the path to our seed sequence file. We need to get your seed sequence files on Hummingbird. The easiest way to do this is to copy the contents of your seed sequence file, go the the terminal window that is connected to hummingbird, type `nano seed.fasta` and hit enter, and use Cmd+V to paste the contents. The nano command created a new text file called "seed.fasta" in your working directory. To exit and save, type Ctrl+X. You will be prompted witht the following message: "Save modified buffer (ANSWERING "No" WILL DESTROY CHANGES) ?" Hit the y key to respond with "yes." You will be prompted again with the message "File Name to Write: seed.fasta". Hit the enter key to confirm the name of the file and the nano program will exit. Congrats, you have just created a text file using the command line interface!
  
  We are now ready to run our assembly software, but running programs on Hummingbird is a little bit different than running programs on our own personal computers. Hummingbird, and all computing clusters, use job scheduling software to organize jobs that are submitted to run. Right now, we are all connected to the login/head node. This node is a server that is great for navigating the file system and creating/modifying directories and text files, but is not where we want to actually run our analysis. If all of us started running code on the login node, we would slow down or crash the system. Instead, we will submit our code to the job scheduler, which will delegate memory and time on other "compute" nodes that are linked to the login node.
  
  To use the job scheduler, we have to create an SBATCH script that specifies the resources we are requesting and includes the code that we want to run. We will title our SBATCH scripts "run_novoplasty.sh."
  
  `nano run_novoplasty.sh`
  
  Copy/paste the following text
  
`
#!/bin/bash
#SBATCH --job-name=novoplasty_assembly
#SBATCH --mail-type=ALL
#SBATCH --mail-user=mglasena@ucsc.edu
#SBATCH --output=novoplasty_assembly_%j.out
#SBATCH --nodes=1
#SBATCH --partition=Instruction
#SBATCH --mem=10GB
#SBATCH --time=2:00:00

/hb/groups/bioe137/NOVOPlasty/NOVOPlasty4.3.1.pl -c config.txt
`
  
  
  
  
