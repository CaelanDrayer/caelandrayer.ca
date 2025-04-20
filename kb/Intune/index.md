---
title: Intune Overview
draft: false
tags:
  - Intune
  - MDM
  - UEM
  - Policies
  - Endpoints
  - Security
  - Device
  - Management
  - Microsoft
aliases: 
description: 
permalink: 
enableToc: true
cssclasses: 
created: 2025-04-13  15:01
modified: 2025-04-13  16:20
published: 
comments: true
---
Intune is Microsoft's unified endpoint management solution. It has spent a few years maturing - and really provides a great feature set. Getting the full use of this feature set can be challenging - and some things are not as intuitive as one would like. I have been using Intune daily for almost 5 years; and have fleshed out some method and design considerations to getting things done. 


As technical folks, we tend to think about all the fancy things we can do with solutions. This is often a mistake due to one simple word - "management". Someone has to manage whatever is setup - and while maybe the person who configured it originally can manage it; others might be left struggling. 

Deploying a solution that is hard to explain, hand-off, or operate daily is generally a bad idea. I would expect that anyone who is doing something like that - has cl