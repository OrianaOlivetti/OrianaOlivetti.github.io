---
layout: post
title: I've read OWASP Web App Pentesting Guide so you don't have to... Part I.
subtitle: 'Web App Pentesting: Information Gathering'
cover-img: /assets/img/owasp-information-gathering/cover.jpg
tags: [owasp, pentesting, guide, webapp, testing, hacking]
comments: true
---

Here I am, as the hero internet needs, to give you all an easy to digest action list of the thick OWASP WebApp Penetration Testing Guide v4. 

The Penetration Testing Methodology is made of 12 different steps an ethical hacker must take when performing a penetration test, and we will go through each one of those sections over time. Being the first one, **Information Gathering**.


The first phase in security assessment is focused on collecting as much information as possible about a target application.
This task can be carried out in many different ways, like using search engines, scanners, sending simple HTTP requests, or revealing the versions and technologies used by the application.

Often it is possible to gather information by receiving a response from the application that could reveal vulnerabilities in its bad configuration or bad server
management.


## Information Gathering List

- Check for the robots.txt file to see any disallowed entries, by visiting **domain/robots.txt**
- Check response **Server** Header for system information and if its version has known vulnerabilities. 
Make any request, look for the server information if available in the response, and search for known vulnerabilities in that particular version.
- Fingerprint application frameworks used by the app to search for known vulnerabilities. This can be done by looking at custom cookies, or the **X-PowerBy** response header
- Send malformed requests to get error responses from the frameworks used. Debugging mode should be disallowed in production environments. This also may disclose the server information and version, as well as some environment varibles.
- Search for meta tags in html responses to identify technologies used, and additional paths/functionality to explore and test. 
For the technologies used, again, search for known vulnerabilities on its versions.
- Check for the sitemap.xml file to see if more information is being disclosed, by visiting **domain/sitemaps.xml** if available.
- Check for security.txt file in **doamin/security.txt** or **domain/.well-known/security.txt** to see if more information is being disclosed
- Check if the source code is not minifyed on the browser. Check for developers’ comments on the source code if available, and also look for variables defined with harcoded values. 

Want to know more? Check the next phase in this post. Happy Hacking! 


























