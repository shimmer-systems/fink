# fink

like the character from o.k. k.o.! i don't own the character nor do i have any affiliation with the creators of the show, i just have a habit of naming my repositories after characters i like. don't ask me why fink in particular, i don't know.

## what is this?

well, [the nix community is kind of imploding, right now](https://github.com/KFearsoff/nix-drama-explained).

ah! great! well, i use nixos quite a bit, unfortunately! so, now that everyone is jumping ship, i have to find a decent alternative, or otherwise make my own system that hits all of the requirements i have.

this repository exists to help me facilitate that.

## what i need

first, it'd be a good idea to define exactly what i want in such a system, before i go wading through the scavengers just beginning to feed on nix's whalefall. 

my ideal system would have and/or be:

#### must-haves

- deterministic
    - one of the main reasons i liked nix in the first place is because it's very set-and-forget. once i've made my config and it works, i know for a fact that it will continue to work in a similar manner, including to an extent across updates - barring any major errors on the part of whoever is maintaining the packages.
- some basic interoperability with existing nix packages, and the things that replace it
    - i don't want to reinvent the wheel here, just my own systems. if i can easily leech off of an existing package repository with minimal issues, i'm going to - i just can't sustain the mental load of manually maintaining my own list of packages.
- support for both building from source, and downloading tarballs from a cache
    - in the same vein as the previous point, i don't want to be forced to build every single package for my system every time it updates. it would take too much time, and it would put unnecessary strain on my already jet engine-like laptop - that's the point of a package cache. that being said, building from source is a very useful tool to have first class support for, especially if the package might otherwise not be maintained in a way that i can use. i don't want to give that up.
- keep the compilation store in some shape or form
    - just like remotely cached tarballs, locally caching the stuff i build for reference in the future is nice. i want to keep that. the weird uuid prepending thing the nix store has right now, maybe not so much. i want to be able to easily tab search for something in the store, even if some sort of hash needs to be included to accurately differentiate different instances of the same version of some package. like, debugging a busted package
- play nice in containers
    - getting nixos working inside of my proxmox containers was an adventure in and of itself, and i want to avoid that particular headache in the future. there's still some unresolved issues with my current working setup that i've just been kicking down the road, because i know how much of a headache they'd be to solve.
- remote deployment, and managing multiple independent systems with the same config files 
    - nix has stuff like colmena for the remote deployment department, and my friend [NotNite](https://github.com/notnite) shared her method of accomplishing something similar, but the common thread was that the systems in question either need to be exposed to the internet, or you need to be on the local network to update them. She got around the issue using tailscale, but there was the tiny issue of the entire process breaking down if tailscale got shut down before the upgrade was mostly complete. any system i'm going to use should, uh, not do that. 

#### nice-to-haves

- addresses my gripes with the current nix system
    - there's been a decent number of stumbling blocks that i've run into over my time using nix and nixos. the biggest one is just how absolutely uninformative the errors are. now, to be fair, a decent part of the reason that i've been getting uninformative errors as of late is that some of the things i do are, in respect to the way the nix language works, capital c Cursed and kind of [cowboy shit](https://www.youtube.com/watch?v=8NrJXvTDd2U). but honestly, i don't think the way i wanted to do things was, with respect to organizing my configs, actually all that unheard of.
    - tl;dr, errors should actually tell you where the program ran into problems. the system should also play nice with me referencing different files within the config itself, like for example listing a directory and then doing some work over the contents of each of the files in that directory. this is where nix sort of broke down for me, and where everyone told me to "just use the module system". like, yes, i get it - the system is the primary way of interfacing with the actual system configuration. the thing is, i WAS using the system, i just wanted to abstract away and modularize parts of it to make it easier on myself. that not being something that i can easily do without making the system totally freak out was, to put it lightly, a bit annoying.
- types
    - this ties into my "gripes with the current system" point, but having types all be completely inferred and you, as the user, being unable to define and specify types means that diagnosing errors becomes that much harder, because the system just assumes that the small error you made is the "intended" thing that you wanted.
- plays nice with pre-compiled binaries
    - having to use patchelf on every precompiled binary just to get it to work gets annoying after a while. i didn't even tend to use precompiled binaries all that often, but it was still annoying. this could be fixed by, as far as i can tell, quite literally just replicating the usual /usr/lib et al. folder schemes that more traditional linux distros use.
- compilation is more robust
    - the number of times a full system upgrade has failed prematurely just because a single git clone failed due to a flaky connection has actually managed to piss me off at times. on certain occasions, i needed to rerun the build command five or six times just to get a particularly large system upgrade to finish properly, even though none of the packages truly failed to build. similarly, while getting fast error messages is nice on some occasions, i want to have the option to just tell it "look, just build everything you can and i'll fix all the errors at the end. i know stuff is broken, i just want to see ALL of what's broken at once". this isn't really a make-or-break feature for me, though, aside from having a basic download retry system.

#### things to think about

- doing something with [dhall](https://dhall-lang.org), since it seems to have a lot of the things i already want in a configuration language. the big question for this is feasibility.
- [this article](https://jade.fyi/blog/flakes-arent-real/), that i just found while writing this. i mainly skimmed it, but the tone of the article suggests to me that it does not shy away from some of the uglier snags of nix that should be avoided.
- [OverlayFS](https://wiki.archlinux.org/title/Overlay_filesystem), which i vaguely remember being talked about in reference to nixos a couple years back. the idea behind this, at least to me, is limiting how much symlinks need to be used...? i'm iffy on this, and honestly don't particularly see the point, but i must have at some point in the past (or i wouldn't have thought of it just now) so i might as well include it here.

## pre-existing alternatives

- as of 04-27-24, practically none. i mean, there's the common culprits like. ansible and puppet and terraform and whatever else. docker and kubernetes and whatnot. not gonna use those.
- some [preliminary work](https://equestria.social/@cloudhop/112344457206234226) being done on a project called [keystone](https://github.com/Fundament-Software/keystone)? this sounds like the developer's ideal solution to governance issues is just to become the governance themself, though. i'll keep track of it, but i can't say i'm particularly invested at this point. no shade to the devs, but the whole point of jumping ship from nix is trust issues and a new project with no established credibility isn't going to cut it for me right now. if i'm using a tool made by someone else, i need to be able to trust that it works the way i want it to, at least most of the time. if i was going to write my own system, it would be something i am writing *just* for me, to cater to my needs - and who else can i trust if not myself?

## conclusion

04-27-24: there isn't much to say, this whole situation is still pretty fresh. i may write my own utility, if it comes to that. i'm going to wait a month or two to see if anything else pans out, first.
