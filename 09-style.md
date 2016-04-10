---
layout: page
title: Automation and Make
subtitle: A few style notes
minutes: 0
---

Make is a very flexible tool for keeping track of dependencies.  It's
therefore not surprising that there's more than one way to write a
Makefile, and no one ‘correct’ way.

The only ordering that matters to `make` is that the first
_non-pattern_ rule in the file (that is, the first rule that doesn't
involve the `%` wildcards) is the default rule.  Apart from that,
you are free to have the rules in almost any order that makes sense
to you.  That said, it's generally a good idea to keep variable
definitions at the top of the file, where you can find them, and
_possibly_ a good idea to keep all of the pattern rules there, too.

There are no targets _required_ in Makefiles, but there are a few
which are conventional.

  * `all`: Often, the first target in the Makefile is `all` (and
    hence the default), which names the actual default target, which
    therefore doesn't have to be located first in the Makefile, if
    it's more sensible for it to be elsewhere.  It means you can
    type `make all` and `make` does The Right Thing.

  * `clean`: It's _always_ a good idea to have a `clean` target.
    Otherwise, it's far too easy, when tweaking your Makefile, to
    mistype `rm *.something` and delete non-generated files.

  * `dist`: Bundling up your data and code, to give to someone else,
    is often quite an intricate process.  Being able to type `make
    dist` takes the anguish out of it.

  * `check`: You _do_ have unit tests, don't you?  In the example in
    this lesson, they would be rather artificial, but in a project
    where you do have them, then whenever you can't think what else
    to do, your fingers should automatically type `make check` so
    you can get the warm glow of seeing all your tests pass.

The real power of Make comes from its ability to keep track of
dependencies; you can do a lot with just that.  GNU Make contains a
lot of functionality beyond ‘traditional’ Make, and it's possible to
use this to create extremely complicated Makefiles.  Don't feel
obliged to use all of this functionality.  When I need to add
sophistication, and where I have a choice, I generally think it's
clearer to add complication to the rules rather than in the Makefile,
since the rules are, after all, mini-shell scripts.

Overall: keep it simple, as much as you can.

Thus, the way _I_ would write the final Makefile is like this:

~~~ {.make}
# Count words script.
COUNT_SRC=wordcount.py

# Plot word counts script.
PLOT_SRC=plotcount.py

# If we need to use a particular version of Python for some reason,
# then we should change this variable,
# or override it with a full path on the make command line.
PYTHON=python


TXT_FILES=$(wildcard books/*.txt)
DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
PNG_FILES=$(patsubst books/%.txt, %.png, $(TXT_FILES))


%.dat : books/%.txt $(COUNT_SRC)
	$(PYTHON) $(COUNT_SRC) $< $@

%.png : %.dat $(PLOT_SRC)
	$(PYTHON) $(PLOT_SRC) $< $@


# The default target is the distribution tarball
all: dist

dist: analysis.tar.gz

# Generate archive file from the distribution directory
analysis.tar.gz: analysis
	tar -czf $@ $^

# Bundle all of the distributed files into a separate directory
# (it's good manners, in a tarball, to have all the contents unpacking
# into a single directory)
analysis: $(DAT_FILES) $(PNG_FILES) $(COUNT_SRC) $(PLOT_SRC)
	mkdir analysis
	cp $^ analysis

check:
	@echo "No unit tests in this project"

clean:
	rm -f $(DAT_FILES) $(PNG_FILES) analysis.tar.gz
	rm -Rf analysis

variables:
	@echo TXT_FILES: $(TXT_FILES)
	@echo DAT_FILES: $(DAT_FILES)
	@echo PNG_FILES: $(PNG_FILES)
~~~~
