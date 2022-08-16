# Web Application Attacks
[Home](../index.md) | [CheatSheets](../cheatsheets.md) | [Theory](../theory.md) | [About](../about.md) | [Back](../theory.md)

## What is a Web Application? 

Web applications consist of many components and allow end users to interact with content in a variety of ways. Some web applications are more complex than others
but in general a web application consists of a web server, web application framework and a database server. In addition, web applications might also make use APIs,
CDNs, load balancers and other application components. 

## Assessing Web Application Security:

When assessing a web application, it is best to start by gathering information about the application itself. Information such as:

* What the web application does.
* What programming languages and frameworks the web application uses.
* What web server the web application is running on.

Is very useful in determining how to approach the web application when exploring it for vulnerabilities. For example, a web app running on an IIS web server typically indicates
that the target is running Microsoft Windows. Once we have a broad idea of what the web application does, we should turn our attention to the underlying
technology stack. The technology stack consists of:

* Programming languages / Frameworks
* Web Server software
* Database Software
* Server OS

While some of this information might have been uncovered in the previous step, it is useful to determine the particular versions of these components in order to identify
existing vulnerabilities. There exists a plethora of tools to identify what technology stack is used by a web application, a list of some useful tools and what they do can
be found below:

* Nikto : Scans multiple web sites and ports to determine the technology stack used by an application | syntax = nikto -h URL
* WhatWeb : Identifies websites, recognises web technologies including content management systems (CMS), blogging platforms, statistic/analytics packages, JavaScript libraries, web servers, and embedded devices.
* Nmap : Port scanning utility that can be used to scan web application using NSE scripts, scripts are specified using the --script flag or using -sC to run all relevant scripts.
* wpscan : WordPress scanning tool that identifies WordPress version, plugins and vulnerabilities.

After scanning a web application and determining the technology stack that it uses, we can begin to fuzz the application for interesting web content that might be exposed.
Application endpoints that are of interest to us are ones that we might not expect to find accessible on the underlying web server, a typical example is discovering an
administration portal accessible at an endpoint such as /admin. Web content such as this can be discovered using a web application fuzzer or by checking the robots.txt file
(if one is present), a fuzzer is a tool that sends HTTP GET requests for different web directories contained within a wordlist to a target application. Commonly used fuzzers include:

* ffuf : Fast Fuzzer you Fool (One of the fastest fuzzers available at the time of writing) https://github.com/ffuf/ffuf
* dirb : A popular web content scanner that is installed as part of Kali https://www.kali.org/tools/dirb/
* gobuster : A tool used to brute force web directories, virtual host names, etc on a web server. https://github.com/OJ/gobuster

Useful wordlists that can be used alongwith these tools can be found at https://github.com/danielmiessler/SecLists under "Web Content".
