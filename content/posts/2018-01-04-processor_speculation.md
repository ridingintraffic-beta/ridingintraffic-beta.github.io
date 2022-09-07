---
layout: single
title: '2018-01-04 processor_speculation '

---
# Processor Speculative Execution 
There is a nasty hardware flaw out there that has been found, which involves just about every cpu in current use, it is essentially an issue with the mem cache on and how out of order processesing happens. which allow a predictive branch to happen where you can predict where something is going to be written. Then you read that location and essentially get a memory shim that sees everything.  
This is known as meltdown and spectre.  They are both pretty nasty, and some folks think that computing is going to be forever changed... mostly because the cpu architecture has just always been done this way

# Links to resources 
[spectre track](https://spectreattack.com/ "spectre track")
[spectre whitepaper](https://spectreattack.com/spectre.pdf "spectre whitepaper")
[meltdown whitepaper](https://meltdownattack.com/meltdown.pdf "meltdown whitepaper")
[reading-privileged-memory-with-side](https://googleprojectzero.blogspot.hr/2018/01/reading-privileged-memory-with-side.html "reading-privileged-memory-with-side")
[cyberus blog](http://blog.cyberus-technology.de/posts/2018-01-03-meltdown.html "cyberus blog")
[meltdown-and-spectre-every-modern-processor-has-unfixable-security-flaws](https://arstechnica.com/gadgets/2018/01/meltdown-and-spectre-every-modern-processor-has-unfixable-security-flaws/ "meltdown-and-spectre-every-modern-processor-has-unfixable-security-flaws")

