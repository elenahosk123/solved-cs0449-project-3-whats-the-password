Download Link: https://assignmentchef.com/product/solved-cs0449-project-3-whats-the-password
<br>
<h1></h1>

A great way to learn how something works is to <strong>break it.</strong> This is especially true for things where you don’t know <em>how</em> they work. Many of the things we know about biology, the universe, physics etc. come from seeing what happens when things go wrong.

The goal of this project is to <strong>recover passwords – or password patterns – for three executable files.</strong> You <strong>don’t have the source code</strong> to these programs, only the compiled executable!

To make it more of a challenge, the programs <em>may</em> have a single, fixed password, or they <em>may</em> use some kind of algorithm to generate/check the password.

Read this section before doing anything else!

Here’s how to set up gdb to use the Intel disassembly syntax. While logged into thoth, do this:

pico ~/.gdbinit

Inside that file, write this exactly:

set disassembly-flavor intel

and save. Now, when you view disassembly in gdb, it will match the slides I gave you and will be way easier to understand overall.

<h2> mystrings.c</h2>

There are many tools that will be helpful for password-cracking. One is a program to extract <strong>readable strings</strong> from a binary file.

A binary file can contain text, but it will be embedded within a bunch of non-text data. Here’s a snippet of an example output of the hexdump -C command, which shows the bytes in hex on the left, and their ASCII version (or . if it’s not ASCII) on the right.

…000002e0  21 00 00 00 12 00 00 00  00 00 00 00 00 00 00 00  |!……………|000002f0  00 00 00 00 00 00 00 00  00 5f 5f 67 6d 6f 6e 5f  |………__gmon_|00000300  73 74 61 72 74 5f 5f 00  6c 69 62 63 2e 73 6f 2e  |start__.libc.so.|00000310  36 00 70 72 69 6e 74 66  00 71 73 6f 72 74 00 5f  |6.printf.qsort._|00000320  5f 6c 69 62 63 5f 73 74  61 72 74 5f 6d 61 69 6e  |_libc_start_main|00000330  00 47 4c 49 42 43 5f 32  2e 32 2e 35 00 00 00 00  |.GLIBC_2.2.5….|00000340  02 00 00 00 02 00 02 00  01 00 01 00 10 00 00 00  |…………….|00000350  10 00 00 00 00 00 00 00  75 1a 69 09 00 00 02 00  |……..u.i…..|00000360  39 00 00 00 00 00 00 00  e8 09 60 00 00 00 00 00  |9………`…..|00000370  06 00 00 00 02 00 00 00  00 00 00 00 00 00 00 00  |…………….|00000380  08 0a 60 00 00 00 00 00  07 00 00 00 01 00 00 00  |..`………….|00000390  00 00 00 00 00 00 00 00  10 0a 60 00 00 00 00 00  |……….`…..|000003a0  07 00 00 00 03 00 00 00  00 00 00 00 00 00 00 00  |…………….|000003b0  18 0a 60 00 00 00 00 00  07 00 00 00 04 00 00 00  |..`………….|000003c0  00 00 00 00 00 00 00 00  48 83 ec 08 e8 7b 00 00  |……..H….{..|000003d0  00 e8 0a 01 00 00 e8 d5  02 00 00 48 83 c4 08 c3  |………..H….|000003e0  ff 35 12 06 20 00 ff 25  14 06 20 00 0f 1f 40 00  |.5.. ..%.. …@.|000003f0  ff 25 12 06 20 00 68 00  00 00 00 e9 e0 ff ff ff  |.%.. .h………|…

There is a UNIX program called strings which lets you search for strings within a binary file like this. You will write a little program called mystrings which will be a very simplified version of strings.

<h3>How it should work</h3>

Your program will be <strong>very</strong> simple. Seriously. This can be done in about 30-40 lines of C, not counting braces.

mystrings will work as follows:

<ul>

 <li>You will run it like ./mystrings somefile.</li>

 <li>It will read somefile as a <strong>binary</strong> file.

  <ul>

   <li>It should check if the file doesn’t exist, by seeing if fopen returned NULL. If so, print an error and quit.</li>

  </ul></li>

 <li>It should look for sequences of 4 or more <strong>printable ASCII characters.</strong>

  <ul>

   <li>It should print each of those sequences on its own line.</li>

   <li>A <strong>printable ASCII character</strong> is a char whose value is in the range 32 to 126, <strong>inclusive.</strong> So [32, 126].</li>

  </ul></li>

</ul>

That’s it.

<h3>Requirements and Tips</h3>

You <strong>don’t have to support</strong> any of the extra options that the strings program supports.

You <strong>don’t have to display tab and newline characters as embedded within strings, like </strong><strong>strings</strong><strong> does.</strong>

The strings are <strong>not necessarily 0-terminated!</strong> They are terminated by <em>any</em> non-printable ASCII character. So, you can’t really use printf(“%s”).

Make sure your program can handle strings that are <strong>arbitrarily long.</strong> Since you have no idea how long a string is when you start reading it, that means you can’t really allocate space to store the whole string.

Fortunately, you only have to <em><strong>print</strong></em> ASCII characters until you hit the end. How many characters do you really have to <em>store?</em> Do you really need to store the string at all??

Try having a look at the functions in the C standard library, such as in stdio.h and ctypes.h. Maybe you’ll find something useful!

<h3>Testing it</h3>

<strong>Do not run it on text files. That’s not what it’s for.</strong> Run it on binary files! Examples include executables, object files, image files, and more. There are lots of executables in /bin, such as /bin/ls.

You can see what your program is <em>supposed</em> to output for a file by comparing its output to the output of the real strings program. I’ve made a shell script for you:

cp /afs/pitt.edu/home/j/f/jfb42/public/cs449/testmystrings.sh .

Have a look inside it, and you will see that it runs both programs and <em>redirects</em> their output to the strings.out and mystrings.out files. Then it uses git diff to compare them.

The &gt; strings.out is <em>redirection</em> – it sends the output of the program to the file strings.out instead of to the console.

Now you can run it in the same directory as mystrings like:

./testmystrings.sh /bin/ls

If it prints nothing, then the files are identical!

<strong>Green lines</strong> mean your program is finding <em>too many</em> strings.

<strong>Red lines</strong> mean your program is <em>missing</em> strings.

<h2>Password-cracking</h2>

I have generated <strong>three executable files for each of you.</strong>

Log into thoth. Then cd /u/SysLab/yourusername, like mine is /u/SysLab/jfb42. (The capitalization in SysLab is important.) There are your executables.

The /u/SysLab/ directory is physically stored on thoth. You cannot access it from any other computer, and any files you put in it will disappear at the end of the term when you lose access to thoth. It’s a better idea to do all your work in your AFS space.

So, <strong>copy the programs to your private directory</strong> by doing cp * ~/private. From there, you can do whatever you want with em.

<h2>Your goal</h2>

When you run these executables, they wait for you to type something and hit enter. You must type the correct password to “unlock” them. The program will tell you whether or not you succeeded.

<h2>OH MY GOD HOW DO I EVEN START</h2>

Reverse engineering is like a puzzle. You have to <strong>start with what you know.</strong> Here are some things to know:

<ul>

 <li><em><strong>A program may have a *different password every time you run it!</strong></em> Make sure to test it several times, on different dates, from different computers etc.</li>

 <li>All the programs are <strong>written in C.</strong>

  <ul>

   <li>The assembly you look at was generated by an algorithm, so there is <em>structure</em> to it.</li>

  </ul></li>

 <li>Each program will have a different password <strong>per student,</strong> but <em>how to find it</em> will be the same for everyone.</li>

 <li>All passwords will be <strong>printable ASCII characters</strong> and be <strong>far less than 100 characters in length.</strong>

  <ul>

   <li>That being said, brute-forcing will probably not be productive.</li>

  </ul></li>

</ul>

<a href="proj3_details.html">Here is a page with a lot more info that might be helpful for you.</a>

<h2>The writeup</h2>

You will be typing up a short document which shows, for each of the three programs:

<ol>

 <li>the <strong>password</strong>

  <ul>

   <li>it might be a <strong>single fixed string</strong></li>

   <li>or it might be <strong>generated by an algorithm,</strong> in which case you must <strong>describe the algorithm</strong></li>

  </ul></li>

 <li>an explanation of <strong>how you found it</strong>

  <ul>

   <li>you don’t have to describe the <em>entire</em> process, but…</li>

   <li>briefly describe any <strong>failed attempts or ideas</strong></li>

   <li>and then describe <strong>how you succeeded.</strong></li>

   <li><strong>If you couldn’t find the password,</strong> you can still get full credit for this part of the writeup by explaining your attempts and your theories on what the password is.</li>

  </ul></li>

</ol>

Please keep it to-the-point and technical. You don’t need an intro and body and conclusion. Just write <strong>clearly and tersely.</strong> Technical writing is about <em>clarity.</em> (Humor and wit are still welcome, of course.)