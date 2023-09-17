---
layout: default
title: The Transformation of a message to a Cloud Lab via AKSH
nav_order: 2
description: An overview of AKSH's architecture and it's internals

permalink: /transformation-of-message-to-cloud-lab-aksh-architecutre
---
# The Transformation of a message to a Cloud Lab via AKSH

-- 


AKSH is a messaging bot that interfaces with a cloud service provider via APIs to automate various aspects of managing labs on their infrastructure. For example, through AKSH, students can deploy a lab in their own AWS account by issuing a single command. If you are visiting this website for the first time, please [read this post](https://aksh.one/#hello-world---love-aksh) for AKSH to formally introduce itself.

In this post, I will explain how AKSH transforms a user's message (or command) to a full-blown cloud lab. However, before we delve into it let's have a closer look at AKSH's internals.

## Core components of AKSH

The following five components spin like gears behind the scene and bring AKSH to [-virtual-] life:
 
 - **Bot Listener** - Receives user's messages (or commands) via the messaging app API.
 - **Lab Management Framework** - Command framework that enables AKSH to understand user's intent and invoke Compute Engine.
 - **Lab Configuration Files (JSON)** - Infrastructure as a Code (IaaC) file, written in JSON, containing a cloud lab's architecture and configuration details. It is used by the Lab Management Framework and Compute Engine to translate user's intent into compute actions.
 - **Compute Engine** - Translates user's intent into compute actions, performs those actions via cloud service provider's API and returns their result.
 - **Compute Action** - A compute action is an operation that will be performed at the cloud servrice provider's end (e.g., launch a compute instance, terminate instance, create snapshots etc.).   

## How the magic happens? 
-- 
![AKSH Architecture](/images/AKSH-Arch.jpg "AKSH Architecture")

--

Here's how AKSH transforms a simple message into a full-blown cloud lab (the above diagram will help you visualize this journey):
 1. **User sends a message** - User uses the messaging app client (mobile or desktop) to send a message to AKSH.
 2. **AKSH receives the message** - The message travels from the messaging app client to the messaging app servers via their API. The messaging app servers delivers that message to the Bot Listener via another set of API.
 3. **Identifies user intent** - The Bot Listener hands-over that message to the Lab Management Framework. The Lab Management Frameworks identifies the user's intent (i.e. the action user wants to perform on the cloud lab - deploy the lab, stop a machine, revert a machine etc.) and the lab configuration to be used. If the user has sent the name of the lab as part of the message, the corresponding lab configuration file will be used. If not, the default lab configuration file will be used. 
 4. **Generates a set of compute actions** - Based on the user's intent and the lab configuration to be used, AKSH generates a set of compute actions. A user intent may result in one or more compute actions.
 5. **Runs the Compute Engine** - AKSH passes the compute actions set to the Compute Engine. The Compute Engine performs the corresponding operation for each compute action at the cloud service provider's end and returns the output (success / fail or a custom value). Compute actions are peformed in an ordered manner. The order of compute actions in a set is determined by the user intent. If any action in the set fails, the entire compute action set is considered as failed. A certain level of resiliency has been built-in into AKSH. It also provides other mechanisms to recover from failure. 
 6. **Infers the result** - Once the Compute Engine has finished performing all compute actions in the set or if the set has failed, it parses the output received from the cloud service provider's end and passes it to the Lab Management Framework. The Lab Management Framework processes the parsed output and hands-over the result message to the Bot Listener.
 7.  **AKSH communicates the result** - The Bot Listner uses the messaging app API to deliver the result message to the messaging app servers. The messaging app server, in-turn, delivers the result message to the messaging app client (mobile or desktop) via API.
 8.  **User receives the result** - The messaging app client notifies the user of an incoming message. User sees the result message and decides what to do (or not to do) next.

## End notes

If I am being honest, there's nothing new happening here. Infact, many of the lab management service providers and training providers probably use a similar (or even better) design. However, those service often come at a price that might not be affordable by everyone. I created AKSH to provide a pocket-friendly option to [my students](https://courses.yaksas.in) to work through a [red team lab.](https://adversaryemulation.com) If you are an instructor in a similar boat, feel free to leverage the above design to create your own lab management setup and reduce dependency on commercial service providers.
