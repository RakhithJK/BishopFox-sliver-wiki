⚠️ __NOTE:__ This guide is intended for absolute beginners to the red team space, please see the [Getting Started](https://github.com/BishopFox/sliver/wiki/Getting-Started) guide for a more straight forward tutorial if that's not you. 

# The Red Team Beginner's Guide to Sliver

⚠️ This page is a work in progress and is incomplete, you may want to read [Getting Started](https://github.com/BishopFox/sliver/wiki/Getting-Started) instead for the time being.

## Introduction

### What's a C2?

A command and control (C2) framework allows attackers to fully leverage existing access to computer systems or networks. C2 frameworks such as Sliver by themselves will generally not get you access to systems that you do not already have. There are many techniques for gaining initial access, however some of the most common are exploiting software vulnerabilities using something like [Metasploit](https://www.metasploit.com/), or by social engineering a user to inadvertently execute a C2 payload. This is referred to as "post-exploitation."

Throughout this guide and the Sliver code base the following terms of art are used. These definitions may vary slightly across the industry but this is how we define them:

* __Implant__ - A generic term for piece of software used to maintain access to an environment or system, generally through the use of command and control (C&C, C2, etc.), this is the code that the attack executes on the target machine as well as maintain access.

* __Beacon__ - May refer to (1) a communication pattern where an implant periodically connects to the C2 server as apposed to using a stateful/real time connection (2) [Cobalt Strike's](https://www.cobaltstrike.com/) primary implant, more often called "CS Beacon."


