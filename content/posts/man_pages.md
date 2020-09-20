---
title: "Writing Man Pages"
date: 2020-09-19
---
I am writing about my experience working with man pages today.
I have never written/updated any man pages in the past. The
man page i updated was [intro(3)](https://www.freebsd.org/cgi/man.cgi?query=intro&sektion=3&manpath=FreeBSD+12.1-RELEASE+and+Ports) of
the FreeBSD Library Functions Manual.

Updating the man page involved going through the FreeBSD source code [src/lib](https://github.com/freebsd/freebsd/tree/master/lib)  and figuring out the libraries that exist.
I came across many libraries which i had never heard of e.g. Performance Counters Library [pmc(3)](https://www.freebsd.org/cgi/man.cgi?query=pmc&sektion=3&manpath=FreeBSD+12.1-RELEASE+and+Ports) :astonished:.And i had to find out which of those libraries have a man page so that they can be listed.

Next was to figure out how do i use the markup ([mdoc(7)](https://www.freebsd.org/cgi/man.cgi?query=mdoc&sektion=7&manpath=FreeBSD+12.1-RELEASE+and+Ports)) to get to the desired formatting. All of this was easy. Few things i have learned during this process.

* Where do the man pages live inside the source code repository?.
  [share/man/man*](https://github.com/freebsd/freebsd/tree/master/share/man)
* The extension of the file is the section in which the man page belongs (e.g intro.3)
* After making the changes, what should i do so that when i type (`man 3 intro`) i see my updates.
  gzip the file (intro.3.gz) and copy the file to relevant section of the manpages on the system `/usr/share/man/man3`.
* How do i check if my changes have any formatting or other issues e.g. date was not updated inside the man page or sentence should begin on a new line.
  FreeBSD has a linting tool [igor](https://www.freebsd.org/cgi/man.cgi?query=igor&sektion=1&manpath=FreeBSD+12.1-RELEASE+and+Ports) to help with these kinds of issues.
  And contributors have to make sure that their changes are clean before submitting a patch.

I realize that documenting foreign code is challenging. I would like to take this opportunity to thank the man pages team for their work :pray:.
