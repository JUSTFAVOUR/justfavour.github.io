---
layout: post
title: "PICOCTF 2024 WRITEUP : SANSALPHA"
date: 2024-03-27
categories: CTF_WRITEUP
---



HMM ,PicoCTF 2024 was fun, given that it was my first live Capture The Flag (CTF) event. My teammates and I were thrilled, although we knew we were not prepared. However, we had to try because hacking is all about learning and having fun while doing it. The CTF was challenging, and we didn't complete all the challenges. One challenge that intrigued me was the last challenge in the General Skills section, "SANSALPHA". It is based on bash, specifically bash injection without using  letters, or "/". This detail was unknown to me from reading the challenge description. When I launched the instance , I could tell it had something to do with bash injection because I remembered watching  a CTF writeup by liveoverflow with the same challenge idea .

For those that know nothing about BASH , there is a simple explaination from our  AI Friend :

```

Bash, short for "Bourne Again Shell," is a type of computer program that acts as a command interpreter or shell for Unix-like operating systems. It's essentially a text-based interface that allows users to interact with their computer by typing commands into a terminal window.

Here's a simplified explanation for a non-technical audience:

Imagine your computer as a house, and the bash shell is like a magic door that lets you talk to the house and ask it to do things for you. You can give the bash shell commands, like "open the door" or "turn on the lights," and it will understand what you want and make it happen.

For example, if you want to create a new folder to store your photos, you can tell bash to "make a new folder called 'Photos,'" and it will create the folder for you. Or if you want to see what files are in a folder, you can ask bash to "show me the files in this folder," and it will list them out for you.

Bash is powerful because it allows you to do many different tasks on your computer just by typing commands, without needing to use a graphical interface like you would with a mouse and keyboard. It's often used by programmers, system administrators, and anyone who wants to control their computer more efficiently.

```

Enough beating around the bush, let's get started with the challenge:
![[Pasted image 20240326174629.png]]

After I logged in through SSH, I was presented with a minimized Ubuntu Terminal. 

![[2024-03-26_17-48.png]]

As you can see, running the `ls` command, we got an error: "Unknown character detected". What do we do now?  
![[Pasted image 20240326192523.png]]

After entering some valid and invalid bash commands, the character `*` gave a meaningful output. But why is this happening? Well, it is because of something called shell expansion. Basically, shell expansion expands variables, commands, and wildcards. So when we typed `*`, we are telling the shell to execute **everything** in that directory. You can read more on shell expansion [here](https://medium.com/@ehoneahobed/shell-expansions-in-linux-what-it-means-and-how-to-take-advantage-of-it-41d471cb02dc).

![[Pasted image 20240326194956.png]]

If we try to go one directory up by using the command `*/*`, using this, we can see a **flag.txt** file. This is interesting, why? Because we have found the location of our flag file. The problem now is to read the content of the file. We could use the `cat` command, but remember, we can't use ASCII letters. So, what we need to do is look for a way to access the `cat` command. Here is the solution:

![[Pasted image 20240326204200.png]]

Now, let's break down each command precisely and straightforwardly:

1. **SansAlpha$ ~**
   - This command attempts to navigate to the home directory (`~`). However, without an actual command before the tilde (`~`), bash interprets this as an argument or input rather than a command, leading to a misunderstanding and the response that `/home/ctf-player` is a directory.

2. **SansAlpha$ $_**
   - `$_` is a special variable in bash that expands to the last argument of the previous command. Since the last argument was `/home/ctf-player`, trying to execute `$_` results in bash again stating `/home/ctf-player` is a directory, as it simply repeats the attempt to navigate or interact with the directory without a proper command.

3. `${_:6:1}${_:12:1}${_:7:1} */????.???` 
   - This complex expression is a clever workaround to execute commands without directly using alphabetical characters, which are apparently restricted in this context. `${_:6:1}${_:12:1}${_:7:1}` extracts and concatenates specific letters from the previous output (`/home/ctf-player`) to form the command `cat` (c-a-t) by slicing the string:
     - `${_:6:1}` gets the 'c' (7th character if you start counting from 0),
     - `${_:12:1}` gets the 'a' (13th character),
     - `${_:7:1}` gets the 't' (8th character).
   - `*/????.???` uses wildcard patterns to specify a file with a four-character name and a three-character extension in the parent directory (`*`), effectively looking for "flag.txt" without having to name it.

4. **return 0 picoCTF{7h15_mu171v3r53_15_m4dn355_4945630a}**
   - This line shows the successful execution of the constructed `cat` command, which displays the content of the file matching the pattern, revealing the flag: `picoCTF{7h15_mu171v3r53_15_m4dn355_4945630a}`. 



TO LEARN MORE  CHECK OUT THIS [VIDEO](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://www.youtube.com/watch%3Fv%3D6D1LnMj0Yt0&ved=2ahUKEwj-ptb6pZKFAxX0WEEAHVYqCagQtwJ6BAggEAI&usg=AOvVaw2nOgft0SoPZujc9js9Vxhx) BY LIVEOVERFLOW .
IF you have question you can send me a mail thanks :)  .
