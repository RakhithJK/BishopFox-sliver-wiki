# Welcome to the sliver wiki!

If you want to use sliver and you're not sure where to start, checkout [getting started](https://github.com/BishopFox/sliver/wiki/Getting-Started).

# What is sliver?

Sliver is a Command and Control (C2) system made for penetration testers. It generates implants (slivers) that can run on virtually every architecture out there, and securely manage these connections through a central server. Sliver supports multiple callback protocols including DNS, TCP, and HTTP to make egress simple. You can even have multiple operators (players) simultaneously commanding your sliver army.

TODO: Network diagram

# Why sliver and not Cobalt Strike / Metasploit / other C2 systems?

Sliver has lots of features that other C2 tools don't have, such as:
* Secure encrypted connections from implant to C2 server. 
    * You'd be surprised just how many hacking tools have bad security.
* Supports multiple operators simultaneously controlling your implants.
* Full audit log on server-side in case things go south.
* Multiple and extensible egress protocols. 
* Tab completion.
* Is fully open source.

# Sure, but what's a "Sliver"?!

Slivers are the second best five-color creature tribe in Magic: The Gathering, of course. Each sliver takes on the abilities of every other sliver, exponentially increasing in strength as they gather. 