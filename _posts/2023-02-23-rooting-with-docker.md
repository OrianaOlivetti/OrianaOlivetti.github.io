---
layout: post
title: Rooting with Docker Containers
subtitle: How to take advantage of container creation to get root access on a host
cover-img: /assets/img/rooting-with-docker/cover.jpg
tags: [docker, root, linux, hack]
comments: true
---

Will you believe me if I tell you that Docker is a hacker? 
Let me show you how to take advantage of Docker containers so you can access any high privilege file or perform any command as root. 

To begin with, if you already know Docker, you might be aware that the processes inside each container are completely isolated from one another, however, they do share the same **system’s kernel**. And if you are new to Docker, now you know it, roughly. 

The fun part starts now!, so if you want to follow along, feel free to duplicate the commands I’m about to show you. Hope you enjoy the ride!
Let’s start by running a sample apache container, but we will put it to sleep for a while. 
~~~
docker run -d alpine sleep 1d
~~~
Next I'm going to search for that process, to see its user.
~~~
ps aux | grep sleep
~~~
![Terminal](/assets/img/rooting-with-docker/1.png){: .mx-auto.d-block :}

Mmm... weird, it says root, not oriana, but it must be the user running inside that container... right? 
It's surely matching the userid from inside the container with the userid that exists on the host machine. But the process inside the container is definetely running with a non-root user. It must be it! 

To clear doubts, let's try another case, to make sure that everything works correctly. 

As root, I’ll create a secret file which is very sensitive, and change its permissions so only root will be authorized to see its content. 
~~~
echo "secret" > /tmp/supersecret.txt
chmod 0600 /tmp/supersecret.txt
~~~
![Terminal](/assets/img/rooting-with-docker/2.png){: .mx-auto.d-block :}

We have our secret file ready, which can only we read and written by root. Meaning that no other user should be able to see it or modify it. 

So now, this less privilege user called simba, (which happens to have the same name as my lovely cat), will create a container adding the directory /tmp as a volume. What will happen inside of it? 
Simba won’t be able to open supersecret.txt, obviously, because he is not a root user. But stuborn as he is, he tries either way. 
~~~
docker run -v /tmp:/tmp -it alpine sh
~~~
![Terminal](/assets/img/rooting-with-docker/3.png){: .mx-auto.d-block :}

Wait, what? How was that possible? Was that some sort of witchcraft?

Not really, but cool stuff isn’t it? This happens because docker daemons usually runs as root on the host system, meaning that every user capable of creating docker containers could revise or alter arbitrary files for which they don’t have permission. Provided that they can mount the directories as volumes, of course. 

And then you realize, that most probably, your docker servers are vulnerable, that you need to warn your colleges. 

**Now, how do we fix this?**

You can make Docker to create user ids and group ids outside the ids range handled by the host system (outside 0-1000) by editing **etc/docker/daemon.json** and adding **{“userns-remap”: “default”}** to the contents on the file. This has some limitation, which you can read and learn more about it on Docker's [official documentation](https://docs.docker.com/engine/security/userns-remap/). And then check the change did the trick by revising the **/etc/subuid** file.

**IMPORTANT**, note that by doing this, all your images and containers will be erased from the system because docker needs to update all the different ids. 

Other solution would be to configure the docker daemon as **rootless**, altough it has several dificulties and much more restrinctions, so make sure you understand what this means if your want to implement this rootless configuration on your system. You can learn more about it in [here](https://docs.docker.com/engine/security/rootless/).

If you liked this post, and want to see Simba putting himself into trouble, make sure to follow me on Twitter and Linkedin, where I’ll update you whenever a new post comes out! 

Happy Hacking! 
