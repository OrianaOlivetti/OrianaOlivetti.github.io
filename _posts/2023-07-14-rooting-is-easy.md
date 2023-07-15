---
layout: post
title: Getting root user access so easily? How come!
subtitle: How to take advantage of system binaries to get root access on a host
cover-img: /assets/img/rooting-is-easy/cover.jpg
tags: [binary, root, linux, hack]
comments: true
---

Oh! Hi! Didn’t see you there little fellow. You may wonder how you can get root access as quick as the blink of an eye… So you are in the right place. 

**What do I mean by root access?**

You may be familiar with the Windows’ Administrator account, that, like our root, is a superuser account designated for system administration purposes. 

What can a superuser account do then? Everything!! 

Wait… what? As you just read, it can perform any task without getting blocked due to the lack of sufficient permission. 

And… Why would we want to gain root permissions? Well… because the sky is the limit! And most probably there are some juicy files and data on the server that we, as CTF players, may want to reach.

**Alright, let’s go to the fun part. Achieving the root user.**

Let’s say you have access to a server, and you just need to get that juicy shadow file to perform a brute force attack. 

So let's say you are stuck in this poor low-privileged user called Simba (Just like my cat, what a coincidence!) and you try to see the shadow file on the server. 

![Terminal](/assets/img/rooting-is-easy/root1.png){: .mx-auto.d-block :}

Oh shut! We don’t have enough permissions to see it! What can we do now?

Well, the **sudo** command has a **--list** option which will show us all the allowed and forbidden commands for the current user. 

![Terminal](/assets/img/rooting-is-easy/root2.png){: .mx-auto.d-block :}

Let’s try it out! 

![Terminal](/assets/img/rooting-is-easy/root3.png){: .mx-auto.d-block :}

As we can see, the interesting part is shown at the bottom of the command output. It says that simba, as a non root user, can execute 3 commands with root permissions: **find**, **less** and **vim** commands, but they don’t seem to be harmful at all!

Not yet, but bear with me for a little longer. 

Now is the time when [this](https://gtfobins.github.io/) amazing database full of binary commands comes into place. As this page says, **“GTFOBins is a curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems.”** And we can use this list as an advantage to get our precious root privilege.

**Let the fun begin**

So, now having our list of root available commands, and the magic list, let’s see how we can make good use of them. 

Let’s start with the **vim** binary. You can now try the privilege escalation yourself alongside simba. 

**1.** Go to the GTFOBins page and look for the **sudo section** of the **vim command**.

![Terminal](/assets/img/rooting-is-easy/root4.png){: .mx-auto.d-block :}

**2.**  There, you will see different commands with vim, that depending on the system, will give us the root access. For now, the **a.** option is enough.

![Terminal](/assets/img/rooting-is-easy/root5.png){: .mx-auto.d-block :}

**3.** Time to try it on our console!

![Terminal](/assets/img/rooting-is-easy/root6.png){: .mx-auto.d-block :}

And just like a magic trick, we made simba disappear and instead make the root account appear! 

Will we be able to see the contents of the server’s shadow file now that we have gain root privileges? There is only one way to find out. And indeed, we now have in our hands what we wanted.

![Terminal](/assets/img/rooting-is-easy/root7.png){: .mx-auto.d-block :}

**Want to keep hacking?** Let’s try it one more time with another command Simba has available. 

This time, Simba will chose to exploit the find binary. 

So, again, we head to the  [page](https://gtfobins.github.io/) to search for the **find sudo section**. 

![Terminal](/assets/img/rooting-is-easy/root8.png){: .mx-auto.d-block :}

And now simba, will again try to exploit this in order to escalate privileges.

![Terminal](/assets/img/rooting-is-easy/root9.png){: .mx-auto.d-block :}

Incredibly simple, right? 

**Conclusion**
So now, you and simba know how to gain root access when a low-privilege user have some binaries available to run as root due to security misconfigurations. How cool is that? It is time to play some CTFs with this trick under our sleeve! 

Happy Hacking! 