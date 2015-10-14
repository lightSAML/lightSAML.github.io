---
title:  "Why writing a new SAML library in PHP"
date:   2013-12-21 15:22:21
tags: SAML
author: tmilos
---

An integration with Microsoft AD request come up. Is that possible at all? After quick googling it turned out it is. 
There it was: simpleSamlPHP, an “award winning” authentication application in PHP. Studying it and experimenting with 
it, I learned a lot about wonderful Oasis' SAML enterprise level authentication protocol and federation. But finally 
when all pieces were assembled in my head, I found it nearly impossible to incorporate simpleSamlPHP into my own 
application.

So, the offer was quite modest, and only one stood out as a possible candidate. An award wining authentication 
application in PHP. Wait… application? But I already have my own app, I need just a library. As it turned out, it 
is quite an application, with it's own routing, modules, autoloading... even templates. That didn't work for me, since
I needed it in a Symfony2 app.

Further investigation of their code reviled there some lib directory. At first it seemed like something that could be
reused to build a Symfony2 security listener. But after some time of trying things out, I gave up. Tightly coupled
components, with calls to static methods, and fixed irreplaceable configuration providers reading config from PHP
files on disk.

Briefly have tried to inject my config trough reflection writing to private fields, and managed to do that, but when
other hidden static dependencies jumped out, I finally gave up, and decided that the PHP community reviven by great
refreshing Symfony2 efforts to bring some order deserves decent reusable SAML library. Hence have started a LightSaml
and SamlSpBundle projects, during which development reading and debugging simpleSamlPHP code have helped me much, and
I'm giving them great credit for that.
