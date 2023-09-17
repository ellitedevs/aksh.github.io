---
layout: default
title: Designing the journey from a command to a cloud lab 
nav_order: 2
description: An overview of AKSH's architecture and how does it work  

permalink: /journey-from-command-to-cloud-lab-aksh-architecutre
---
# Designing the journey from a command to a cloud lab

-- 
![AKSH Architecture](/images/AKSH-Arch.jpg "AKSH Architecture")

AKSH is a messaging bot that interfaces with cloud service provider's via APIs to automate various aspects of managing labs on their infrastructure. For example, through AKSH, students can deploy a lab in their own AWS account by issuing a single command. If you are visiting this website for the first time, [read this post](https://aksh.one/#hello-world---love-aksh) for AKSH to formally introduce itself.

In this post, I will explain the journey of a user's message (or command) to a full-blown cloud lab. However, before  we delve into it let's have a closer look at AKSH's internals.

## Core components

The following four components act like AKSH's gears that spin behind the secene and bring it to life:
 
 - sfsd
