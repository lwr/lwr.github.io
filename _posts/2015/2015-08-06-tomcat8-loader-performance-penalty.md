---
layout: post
tagline:
category: programmer
tags:
published: true
title: Tomcat 8's Performance Penalty
---

A really simple project: <https://github.com/lwr/tomcat8-custom-loader>


## What's the problem

You should read this thread (Warning: TL;DR)

> <https://issues.apache.org/bugzilla/show_bug.cgi?id=57251>

Conclusions:

- Tomcat 8 has a sensitive startup performance penalty if you use `unpackWARs="false"`
- Mark Thomas (from Tomcat) do not like this options
- They do not plan to solve this problem, and leave a guide as

  > - use unique appBase values per Host
  > - place the WARs outside all appBases
  > - use context.xml files to add apps to a Host
  > - leave unpackWARs as the default of true
- They even want to remove it in future

## What it solves

Now use a custom loader from this project (currently 0.5) fix some problems:

- Startup performance
  Testing on a little test war from springframework about 7MB, the loading time

  - Using default loader: about 31 seconds
  - Using custom loader: about 8 seconds
- CustomClassLoader's `getURLs` returns `file:yy.jar` URLs instead of `jar:xx.war!/WEB-INF/yy.jar` URLs

  That helps discovering classes in apps more easily

## How to use

Build the project first, using

```bash
mvn package
```
or

```bash
mvn install
```

Then

1. drop the jar from target to your tomcat's `${catalina.base}/lib`
2. edit `${catalina.base}/conf/context.xml`
3. add this line to `context.xml`

   ```xml
   <Loader loaderClass="com.github.lwr.tomcat8.CustomWebappClassLoader"/>
   ```

   (after edited)

   ```xml
   <!-- The contents of this file will be loaded for each web application -->
   <Context>
       ... ...

       <!-- Bellow is the added new line -->
       <Loader loaderClass="com.github.lwr.tomcat8.CustomWebappClassLoader"/>
   </Context>
   ```
4. start / restart your tomcat to check the load time

## About hacking and maintainability

It is not a perfect project, and it uses some hacks such as

* `StandardRoot.classResources` is immutable, so reflection is used for modify it's content
* `JarWarResourceSet.archivePath` is not accessible, so reflection is used because there no other choice

And Tomcat do not guarantee any undocumented API changes so that they could be change eventually, so this workaround maybe broken in the future. I will try to fix it if this occurs.

I hope they will change their mind to bring back this *NON-FEATURE* (expand all jars while `unpackWARS=false`)
