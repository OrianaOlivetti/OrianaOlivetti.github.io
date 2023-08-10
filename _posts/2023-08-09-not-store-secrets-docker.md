---
layout: post
title: 3 Ways NOT to store secrets in Docker Containers
subtitle: Can your secrets be compromised in a containerized deployment? Are they safe enough from no desired snoopers?
cover-img: /assets/img/not-store-secrets-docker/cover.jpg
tags: [docker, containers, secrets, leak, envars, volume]
comments: true
---

**What do I mean by "secrets"?**

Passwords, API Tokens, Certificates... You name it. Whatever information your code needs to access what it needs to access - lovely redundancy :) -. 
Your webserver maybe needs a database password so that it can access a table to do whatever it needs to do. And we want to make sure that we can give that information to the code without anybody else getting to see it. - So sorry, this is private my lads! -

And as an aside note I like to **encrypt my secrets**.
Why do I want to keep them encrypted? -> Well, if somebody somehow gets access to your cluster.... gets access to one of your machines... gets access to one of your containers... If the secret is in plain text the job is done, otherwise, if the secret is encrypted they still have a challenge ahead of them. Kind of obvious.

**Now let's think about the practicalities of how we actually get a secret into a running container...**

I'll start with some really **bad ways.**

**#1.** The bad ways are to slide the secret value into the code somehow. Probably half of you -my lovely readers- say, *"Of course that's stupid"*, and half of you are going, *"Hmm, yes, no I've never done that. No, don't look at me."* :B 

In case it's not that obvious to you, if you have your secrets in your source code, you've made those secrets accessible to everybody who can see the source code. 

Open source? -> That's a really bad idea. 

Closed source? -> You don't necessarily want all of your developers to be able to read all your secrets

The other thing is that it forces you to update your deployed code every time you need to modify or create a secret. And that's probably not a good idea. 

*<<If you're the security person who wants to wake up a developer to say hey, it's 3:00 in the morning but I need you to deploy a new secret. Then you aren't going to be very popular. So DON'T put your secrets in the source code.>>*


Then, if they're not going to be in the container image, we're going to have to get them in at run-time. 
And there are two methods for doing so...

**#2.** One is with **environment variables.**

So let's actually create a container... 
~~~
docker run -it --rm --name ubuntu -e MYSECRET=admin123 ubuntu /bin/bash
~~~
![Terminal](/assets/img/not-store-secrets-docker/docker1.png){: .mx-auto.d-block :}

As I didn't have the latest ubuntu image, it was downloaded for me to use while trying to get the container running.
~~~
env | grep MYSECRET
~~~
![Terminal](/assets/img/not-store-secrets-docker/docker2.png){: .mx-auto.d-block :}

Obviously, as you would expect, that value is in the container's environment. Anybody who can **exec** into that container will also be able to read every secret.

And in case you don't know. It's also accessible from a couple of other places. So someone could, for example, inspect that container...

~~~
docker inspect ubuntu -f "{{json .Config.Env}}"
~~~
![Terminal](/assets/img/not-store-secrets-docker/docker3.png){: .mx-auto.d-block :}

And we can see that anybody who can run **docker inspect** - possibly remotely, not necessarily from the exact same machine - can see that environment very clear.


Also, I'm just going to send this container to sleep for a little while so that I can find its process-id inside that container. 
~~~
sleep 100 #(Inside the Container)
~~~
![Terminal](/assets/img/not-store-secrets-docker/docker4.png){: .mx-auto.d-block :}

~~~
pidof sleep #(Outside the Container)
~~~
![Terminal](/assets/img/not-store-secrets-docker/docker5.png){: .mx-auto.d-block :}

Now I need to be **sudo** or **root** to do this, but inside the **/proc directory**, there is a ton of really interesting information about running processes... Including the environment!!
~~~
sudo cat /proc/<pid>/environ | tr "\0" "\n" #(Outside the Container)
~~~
![Terminal](/assets/img/not-store-secrets-docker/docker6.png){: .mx-auto.d-block :}

And right at the top, there is my super-secret secret.

But perhaps the most compelling reason to not use this mechanism is that environments are logged a lot. Quite often when anything goes wrong it will dump the environment to some log file which now contains all your secrets clearly accessible to anybody who can read those logs. So for that reason, you should use the alternative which is to…

**#3.** **Mount a volume.**

*"But wait, Oriana"* I heard you say, *"NOT TO write your secrets onto disk. They have to be only unencrypted in memory."*
And yes, that's right! But those volumes are **temporary file-systems**. A temporary file-system looks like a file-system.. it has things that look like files and directories, but it's actually only held in memory, not on disk. - Point for volumes! - 

**And... How does this compare in terms of ways that people can get to those secrets?**

* Well, the docker inspecting goes away. If I run **docker inspect** I can see the fact that volumes are mounted but you don't automatically get a dump of all the files inside those volumes. So people can't just easily grab your secrets by running docker inspect. **:)**

* Anybody who can **exec** into the container can, of course, read that secret. **:(**

* The **/proc directory** thing is still a risk - again only for people who are on your host... so probably, you have other concerns - But you can read the root file system of a container from inside that directory. **:(**

* Another thing that does go away is the **logging** problem. You're unlikely to see routine logs of all the files with their content. **:)**

**Conclusion**

So having a mounted volume holding your secret is probably a slightly more secure way of going about it than an environment variable. There're still risks either way.

When you're thinking about secrets, like everything in security, there's no perfect solution. All you can do is reduce the risk and prioritize the system's needs. 

Happy Hacking!