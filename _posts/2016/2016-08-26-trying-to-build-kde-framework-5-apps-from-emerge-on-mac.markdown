---
layout: post
title: Trying to build KDE Framework 5 apps from Emerge on Mac
date: '2016-08-26 02:24'
---

So I was interested in checking out the new [KDevelop 5.0](https://www.kdevelop.org/news/kdevelop-500-released) on Mac.
Not really any particular reason, just wanted to see how KDE's Mac stuff had improved,
and the Python editor seemed like it might be interesting.
The build process started getting somewhat involved, so I figured I should document it for future use.
I'm writing this as I go, making page breaks as I come back to write something new,
so apologies if it ends up a mess and if there's any omissions.

Do note that they apparently [hope](http://kfunk.org/2016/08/23/whats-new-in-kdevelop-5-0/) to put out an official Mac build,
so they may have put one out when you read this. Also these instructions may stop working
or be unnecessary as KDE updates their code (this post is roughly around KDE Frameworks 5.25.0).

---
Following the [official build instructions](https://community.kde.org/Guidelines_and_HOWTOs/Build_from_source/Mac), I installed QT 5.7 from the official installer,
and assumed the Python 3 I had installed from Homebrew previously would work. I haven't tried with Homebrew's QT5,
but I noticed it was a version behind at the time of writing, and I figured I didn't want to mess with build flags.

I then ran into the following error trying to run kdeenv.sh:

    ssh: Could not resolve hostname kde: nodename nor servname provided, or not known

Turns out this is easy to web search, and I ran into this [solution](https://forum.kde.org/viewtopic.php?f=230&t=123865). Thank you forum user GDR!.

```shell
    # ~/.gitconfig
    [url "git://anongit.kde.org/"]
    insteadOf = kde:
```

Seems like there should be a better place to put this? I only need it for emerge. \*shrug\*

---
I also installed GNU coreutils with Homebrew, since emerge/kdeenv.sh couldn't find the realpath command.
Pretty simple. I added the "normal names" path to the build PATH, but it turned out
Homebrew adds a non-prefixed version of realpath to /usr/local/bin anyway.

---
One of the dependencies needed Ki18n, and that needed libintl, which it was not finding.
I had to set the CMAKE_INCLUDE_PATH and CMAKE_LIBRARY_PATH to gettext's corresponding Homebrew locations.
I set some other similar flags, like LDFLAGS and C_INCLUDE_PATH, forgetting how
I solved include errors before on other projects, so I'm not sure if that affected the build.

I also needed to install a newer version of GNU Bison and add it to the build PATH.
The system bison was complaining about a `%code` directive, which apparently was added in 2.3b or 2.4?
I'm not sure. Anyway, Mac's bison 2.3 doesn't seem to know about it, so I installed bison 3.0 from Homebrew.

---
I'm stuck trying to figure out the simplest way to get Qt5WebKitWidgets for kdevplatform.
It looks like it was last bundled with QT 5.5, but even if I did want to download that for this, KDE recommends 5.6+.
Also looks like the project has been removed from Webkit's code, so it seems I'd have to dig up
an older snapshot in order to build that component myself. I already wasn't quite willing to build Webkit for this.
I was gonna see if I could quickly trick CMake into skipping the check just to see what would
happen, but the effort didn't seem worth it after a weak initial attempt.

Well, Kate/KWrite build alright. Makes a nice app and everything. A little newer than the official Mac [preview builds of those](https://kate-editor.org/get-it/) too.

![The Kate editor's looking pretty good]({{ site.baseurl }}/assets/KateMacWoo.png)

Konsole got built at some point too. It's got a goofy drop-down menu fade that is surprisingly noticeable versus
Mac's default menu behavior, which I remember looking better without that contrast. So that's fun. 
(I usually use the fish shell, but I'd have to change the colors to get a readable prompt, so outdated bash screenshot)

![Konsole!]({{ site.baseurl }}/assets/KonsoleMac.png)

I'm a bit disappointed that none of the games appear to be in emerge. Oh well.
Tried checking out a few other interesting packages to test it out, but they seemed to fail in weird seemingly random ways.

---
Well, got most of the way there. Looks like for KDevelop it might be more worth it to figure out
whether QtWebKitWidgets can be removed or replaced for this build.
Still, most of the actual build stuff seems to be working.
Gonna call this post here and maybe look at building KDevelop later.
