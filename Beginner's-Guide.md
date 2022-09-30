⚠️ __NOTE:__ This guide is intended for absolute beginners to the red team space, please see the [Getting Started](https://github.com/BishopFox/sliver/wiki/Getting-Started) guide for a more straight forward tutorial if that's not you. 

# The Red Team Beginner's Guide to Sliver

⚠️ This page is a work in progress and is incomplete, you may want to read [Getting Started](https://github.com/BishopFox/sliver/wiki/Getting-Started) or the [Community Guides](https://github.com/BishopFox/sliver/wiki/Community-Guides) instead for the time being.

## Introduction

### What's a C2?

A command and control (C2) framework allows attackers to fully leverage existing access to computer systems or networks. C2 frameworks such as Sliver by themselves will generally not get you access to systems that you do not already have. There are many techniques for gaining initial access, however some of the most common are exploiting software vulnerabilities using something like [Metasploit](https://www.metasploit.com/), or by social engineering a user to inadvertently execute a C2 payload. Regardless of the initial access method, the subsequent steps taken by an attacker are referred to as "post-exploitation" activities.

Throughout this guide and the Sliver code base the following terms of art are used. These definitions may vary slightly across the industry but this is how we define them:

* __Implant__ - A generic term for piece of software used to maintain access to an environment or system, generally through the use of command and control (C&C, C2, etc.), this is the code that the attack executes on the target machine as well as maintain access. The term "implant" is often interchangeable with "agent."

* __Beacon__ - May refer to (1) a communication pattern where an implant periodically connects to the C2 server as apposed to using a stateful/real time connection (2) [Cobalt Strike's](https://www.cobaltstrike.com/) primary implant, more often called "CS Beacon." In the context of Sliver, "Beacon" specifically refers to a Sliver implant that implements definition (1) communication style.

* __Stage__ - A "stage" or "staged payload" is a method of loading a piece of code, typically over a network, onto a remote machine. Traditionally staging was used in conjunction with exploits that had size limitations. Typically the goal is to execute a small piece of code, the "stager," which in turn loads a larger piece of code. Staged payloads are still used in this way today, however they are also often useful for bypassing anti-virus detection since the majority of the malicious code is loaded directly into memory. Stages are sometimes number e.g. a "stage 0" will load a "stage 1" which loads a "stage 2" payload.

#### Other C2s



