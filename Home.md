# Welcome to the sliver wiki!

If you want to use sliver and you're not sure where to start, checkout [getting started](https://github.com/BishopFox/sliver/wiki/Getting-Started).

## What is sliver?

Sliver is a Command and Control (C2) system made for penetration testers, red teams, and advanced persistent threats. It generates implants (slivers) that can run on virtually every architecture out there, and securely manage these connections through a central server. Sliver supports multiple callback protocols including DNS, TCP, and HTTP(S) to make egress simple, even when those pesky blue teams block your domains. You can even have multiple operators (players) simultaneously commanding your sliver army.

TODO: Network diagram

## Why sliver and not Cobalt Strike / Metasploit / other C2 systems?

Sliver has lots of features that other C2 tools don't have, such as:
* Dynamic code generation with per-binary obfuscation
* Secure encrypted connections from implant to C2 server. 
    * You'd be surprised just how many hacking tools have bad security.
* Supports multiple operators simultaneously controlling your implants.
* Full audit log on server-side for your white team.
* Multiple and extensible egress protocols. 
* Tab completion.
* Is fully open source.

## Sure, but what's a "Sliver"?!

Slivers are the best five-color creature tribe in [Magic: The Gathering](https://magic.wizards.com/en), of course! Each sliver is connected to a hive mind and takes on the abilities of every other sliver, exponentially increasing in strength as they gather. 

![Sliver](https://raw.githubusercontent.com/BishopFox/sliver/master/.github/images/sliver.jpeg?token=AAGVUDRDHPMSBO5JFR3Q7RC5BL2YA)