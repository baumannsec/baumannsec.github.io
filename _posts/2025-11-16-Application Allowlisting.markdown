---
layout: post
title:  "Application Allowlisting"
date:   2025-11-16 19:30:00 -0700
categories: Basics
---

# Application Allowlisting 

## Allowlisting Beats Denylists

Security teams have relied on deny lists for decades. The logic seems simple: identify the bad, block the bad, stay safe. The problem is that there will always be more bad than good, and adversaries are constantly generating new variations faster than any deny list can keep up.

Humans are wired for this failing. Since the beginning, survival meant spotting danger and avoiding it. Cybersecurity inherited this same mindset — focus on the bad, blacklist the bad. Unfortunately, attackers know this, and they innovate around it every day.

Application allowlisting reverses the model. Instead of chasing endless malware variants, you control where code is allowed to run and what is allowed to run. Before we get to that, let’s walk through why deny lists and traditional AV/EDR can be evaded so easily.

**Why Denylists Fail**

Major endpoint products — even the best ones — are fundamentally reactive. They look for:
- Known malicious signatures
- Known patterns
- Known behaviors

Attackers simply modify their tooling until none of those detection rules apply.

## Ghostwriting (Signature Modification)

Antivirus engines detect malicious executables based on signatures. Tools like Metasploit generate payloads that AV vendors learn to recognize. *“Ghostwriting”* is the process of taking that payload and altering it — not the functionality, but the portions of the binary that create those recognizable signatures.

As long as the underlying logic stays intact, the payload will still work, but the AV engine won’t recognize it.

**Understanding the Executable**

So how do attackers alter the payload without fundamentally changing the functionality? To answer that question, let's begin with an oversimplified explanation of a computer program. 

A program is composed of blocks — sections of assembly that perform specific operations. One block calls another, which calls another, and eventually the kernel executes those instructions.

AV and signature-based products monitor the execution of programs and watch for:
- How the binary invokes the kernel
- What malicious hooks are used
- What modules it interacts with

But if the attacker restructures the code blocks, adds noise, or manipulates the assembly, those monitored patterns break.

**A Basic Modification Workflow**

So here is another oversimplified example of how to do a basic modification.

1. Compile an .exe

2. Convert it to an .asm file

3. Modify the assembly code

4. Recompile back into an .exe

Metasploit even provides tooling for this (such as metasm.rb).

**Assembly Tricks: *XOR & Garbage Instructions***

An example of XOR:

Take two passwords — one entered by the user and one stored on disk. If you do an XOR comparison of the password that was entered with the password in storage the output will produce a 0 if they match. If they differ, the result will !=0. 

Why this matters to an attacker:
XOR is also useful for manipulating registers, which is an area of memory that the CPU can access extremely fast. If you XOR a register with itself, it always becomes zero, and by doing so, clears out the register. 

This gives attackers a reliable “cleanup” instruction.

Everything before that line is fair game. They can add:
- Junk instructions
- Stack adjustments
- Meaningless arithmetic
- Randomized sequences of operations

Because the final XOR resets the register, the program still works — but the binary’s structure becomes unrecognizable to AV. Therefor, using specific string and block pattern matching detection does not work. 

**Polymorphic Encoding**

A polymorphic encoder takes a payload and encodes it in a way that:
- Preserves the functionality
- Changes the binary output on every iteration
- Same executable - Completely different bytes on disk - Signature matching becomes useless.

## Bypassing AV With LOLBins

Another evasion technique relies on using what is already there on the system, Living Off the Land Binaries (LOLBins) — executables already built into Windows. This technique still requires initial access compromise. 

Tools like:
- certutil.exe
- rundll32.exe
- installutil.exe

can run payloads, download files, decode base64, load DLLs, intall/uninstall, etc. 

These tools are trusted, signed by Microsoft, and whitelisted by default. If an attacker gains initial access, they can bypass AV entirely by abusing what’s already on the system. 

## Know Thy Directories

Instead of spending time trying to focus on identifying what's bad and all the different types of bad, it all comes down to that initial code execution. 

Where is code allowed to run from?

Directories that malware likes to execute from:
- Downloads
- Temp folders
- User-writable directories
- Documents
- Desktop
- %LocalAppData% 

The question we must ask ourselves is - Do legitimate programs need to execute from these locations?
The answer - Almost never. *see further below for a possible reason why a executable might need to*

Not allowing code to execute from these directories will shut down a lot of the initial attack vectors for workstations. *the same allowlisting concept can be used on plugins for browsers*

Ok, so where does legitimate programs execute from? 

Directories that legitimate code runs from:

Linux:
- /usr/bin
- /usr/local/bin

Windows:
- Program Files
- Program Files (x86)
- C:\Windows\System32

Set it up to only allow programs to execute from these specific directories and no where else. 

**Additional Allowlisting Methods**
1. Allowlisting by Hash

Highly restrictive but extremely secure. Perfect for kiosks, labs, or systems with a fixed purpose.

2. Allowlisting by Publisher

Windows AppLocker can create custom rules for specific manufacturers to be allowed to execute on a system.

For example:
Take an organization that uses Slack. An application like Slack will often download its own updates and extract executables into user-writable directories, such as %LocalAppData%\Slack. By using AppLocker, you can create a Publisher rule for this specific vendor that allows executables signed by Slack's digital certificate and signature to execute, even from a user writable location. 

But, make sure you test thoroughly. While Slack signs their executables, many vendors do not sign every DLL or component they ship.

**Conclusion**

Deny lists fail because attackers only need to modify the malicious code slightly to evade detection. With assembly tweaks, junk instructions, polymorphism, and LOLBin abuse, adversaries can easily slip past signature-based defenses.

Application allowlisting shifts the model entirely: instead of trying to detect badness, you enforce exactly what is allowed to run and from where. It’s one of the most effective controls you can deploy — especially in environments where uptime and integrity matter.