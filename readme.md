# `package-manager`
## Introduction
This is `package-manager`, an alias for the package manager, on any system. Well, actually, since the implementation of this idea is trivial (and to a certain extent system-dependent), I have not bothered with the implementation, and instead merely describe here the problem and solution, in the hopes that other people will converge around this idea, or I can otherwise make it happen in the future. (There may be a need to make shell scripts or other programs to support this idea in the future, which I may put here if I make them.)

So, the problem: often, when giving a set of instructions in a tutorial or shell script to set up a given computer program or workflow, all the steps are are same except for the first one, which is installing the software from a package manager. Guides and scripts then spend a lot of logic directing you to perform the right set of operations in the right package managers. I think it would be much cleaner if there were a single command for basic operations like this that would simply dispatch to the correct package manager on your system. You'd still have your real package manager around for complex operations and system organization, but `package-manager` would support these simple operations:

## Commands

```
package-manager install
package-manager uninstall
package-manager update
package-manager show
package-manager search
package-manager help
```

each command being followed by any number of package or file name(s).

## Caveats, details

A fuller specification of these commands could be written, but as for the possibly-unintuitive stuff: `uninstall` removes the program completely, including its configuration files. `update` installs the latest version of the package available (`install` will simply tell you that you already have the package installed if you try to install it again.), also pulling the updated index of packages from the package manager sources if need be. `help` shows the help message for package-manager, explaining the concept of package managers and the fact that it is just a dispatcher, the name and version of the package manager it dispatches to, and then (perhaps) the help for the underlying package manager.

For cross-system compatibility and scripting reasons, `package-manager` must need not be preceded or accompanied by any other command (such as `sudo`) and must not prompt for confirmation (eg "are you sure you want to install the package you just said you wanted to install?"). If it is technically infeasible to remove all prompts from `package-manager` (such as solicitation for credentials), they must be presented instantly, in the first 500ms of `package-manager`'s run. This guarantee should hold in reasonable circumstances. This is meant to prevent infuriating situations where you run a program, then leave, then come back to find it hasn't achieved anything because it's prompting you for something in the middle.

`package-manager` should run its operations in parallel if possible.

## Musings

Install could also take versions appended to the package names? See https://semver.org/

Perhaps the ideal name for this tool would not be `package-manager` but `install`; alas, there is already an `install`. It seemingly just moves files around and chmods them... thus saving you one line of shell script, I guess. See https://en.wikipedia.org/wiki/Install_(Unix) . Update: `install` isn't specified in POSIX (and "It has mostly split into two camps in terms of compatibility") so I don't know if we need to be precious about name-colliding with it. However, in my more-recent thinking, I have begun to like the name `get` in lieu of `package-manager install`; what could be simpler? And, somehow, there isn't already a widespread program called `get`. `unget` is also a good name for uninstall.

It would be best if this alias were to be adopted by everyone, so it could always be counted upon (within reason), thus solving the problem once and for all. Perhaps it could eventually make its way into POSIX or Bash built-ins or something.

## The package naming problem

Like babel before us, computing has become a confusion of tongues. Not all packages necessarily have the same name in all package managers' systems. Also, not all packages have the same name as the programs you then invoke from them on the command line! What a predicament! So, how can package-manager resolve this conflict? There are various approaches to decide between:
1. Do not attempt to resolve the conflict. Simply dispatch to the package manager and rely on the user to know the right package on the system. This is very easy to implement, and probably does not lead to much real-world harm. In most present-day bad cases, one could simply attempt multiple `install`s. If a package is known as examplepackage on one system and example-package on another, simply try to install both and at least one will work. (Obviously, this creates an increase in security risk.)
2. Maintain a complex many-to-many mapping of equivalent package names. Unfortunately, this does not work with full generality because there is the potential for ambiguous situations.
3. Prompt the user to ask them to clarify. This might work if the package descriptions are good enough. Of course, this is just equivalent to other possibilities, if done non-interactively and the error output of the failing `install` gives "did you mean...?" hints. And, as stated above, I do not wish `package-manager` to ever prompt.
4. Mandate a canonical name(s) for each package, (which I hope dearly would, in all cases, be) the name that the command is called on the command line. This is good practice anyway, since you don't want commands on your computer to be able to have the same name.
5. Simply refuse to execute on ambiguous cases, forcing the user to use their actual package manager for those. This would only affect a handful of case, and is a pretty good general policy for a computer program.

I favor 4 but could see 2 working for some trivial details. 5 is a fine stop-gap measure. 1 is the only one that can be done automatically. I would have to do more research on current package managers to really determine the right move here—perhaps there are some details that I'm not thinking of. The persons who implement `package-manager` for real will probably have some more insight to the situation.

## The proliferation of programming language package managers
In the document above, "package manager" means to refer to the operating system level package manager (ospms), like apt and rpm. It does not include programming-language-specific package managers (plpms) like pip and cpan. This is for several reasons, including:
1. There are many more plpms and they generally have a much lower barrier to entry (such as allowing anyone to upload something, with no vetting). This creates myriad more frustration in handling them, probably.
2. To a first approximation, every ospm can download every plpm, so if you need the latter you should just be able to `package-manager install my-plpm; plpm install my-library`

## Why not simply use `curl` or `wget` to install software?
🧐 <!-- Note for posterity: this emoji is supposed to indicate a man with a monocle looking afronted by your suggestion, as though you have just suggested to a gentleman a solution below his station. However, there is currently no way to enforce that this emoji render with that expression; it's just the case that on current systems, as I write this, it usually does. If there is a way to enforce that in the future, like a recommended ZWJ sequence, I will try to edit this readme to use it. -->

For real, though, I'm fine with considering other software-installation paradigms, and their pros and cons, but `package-manager` is meant to address a problem within the operating system package manager paradigm, so it isn't concerned with other methods like these.
