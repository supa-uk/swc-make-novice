---
layout: page
title: Automation and Make
subtitle: Solutions
minutes: 0
---

## Lesson 02-makefiles

> ## Write two new rules {.challenge}
>
> Write a new rule for `last.dat`, created from `books/last.txt`.
>
> Update the `dats` rule with this target.
>
> Write a new rule for `analysis.tar.gz`, which creates an archive of
> the data files. The rule needs to: 
> 
> * Depend upon each of the three `.dat` files.
> * Invoke the action `tar -czf analysis.tar.gz isles.dat abyss.dat
>   last.dat` 
>
> Update `clean` to remove `analysis.tar.gz`.

~~~ {.bash}
# Generate archive file.
analysis.tar.gz: isles.dat abyss.dat last.dat
    	tar -czf analysis.tar.gz isles.dat abyss.dat last.dat

# Count words.
.PHONY : dats
dats : isles.dat abyss.dat last.dat

isles.dat : books/isles.txt
    	python wordcount.py books/isles.txt isles.dat

abyss.dat : books/abyss.txt
    	python wordcount.py books/abyss.txt abyss.dat

last.dat: books/last.txt
    	python wordcount.py books/last.txt last.dat

.PHONY : clean
clean :
    	rm -f *.dat
    	rm -f analysis.tar.gz
~~~

## Lesson 03-variables
> ## Update dependencies {.challenge}
> 
> What will happen if you now execute:
> 
> ~~~ {.bash}
> $ touch *.dat
> $ make analysis.tar.gz
> ~~~
> 
> 1. nothing
> 2. all files recreated
> 3. only .dat files recreated
> 4. only analysis.tar.gz recreated

* * *
4.) only `analysis.tar.gz` recreated. 

You can check that `*.dat` is being expanded in the target of the rule for
    `analysis.tar.gz` by echoing the value of the automatic variable `$^` 
    (all dependencies of the current rule).

~~~ {.bash}
analysis.tar.gz: *.dat
    @echo $^
    tar -czf $@ $^
~~~
The rules for `*.dat` are not executed because their corresponding `.txt` files
haven't been modified.

If you run:

~~~ {.bash}
    $ touch *.dat
    $ touch books/*.txt
    $ make analysis.tar.gz
~~~

You will find that the `.dat` files as well as `analysis.tar.gz` are recreated.

* * *



> ## Rewrite `.dat` rules to use automatic variables {.challenge}
>
> Rewrite each `.dat` rule to use the automatic variables `$@` ('the
> target of the current rule') and `$<` ('the first dependency of the
> current rule').

~~~ {.bash}
# Generate archive file.
analysis.tar.gz: *.dat
    	tar -czf $@ $^

# Count words.
.PHONY : dats
dats : isles.dat abyss.dat last.dat

isles.dat : books/isles.txt
    	python wordcount.py $< $@

abyss.dat : books/abyss.txt
    	python wordcount.py $< $@

last.dat: books/last.txt
    	python wordcount.py $< $@

.PHONY : clean
clean :
    	rm -f *.dat
    	rm -f analysis.tar.gz
~~~


## Lesson 06-variables

> ## Use variables {.challenge}
>
> Update `Makefile` so that the `%.dat` and `analysis.tar.gz` rules
> reference the variables `COUNT_SRC` and `COUNT_EXE`.

~~~ {.bash}
COUNT_SRC=wordcount.py
COUNT_EXE=python $(COUNT_SRC)

# Generate archive file.
analysis.tar.gz : *.dat COUNT_SRC
    tar -czf $@ $^

# Count words.
.PHONY : dats
dats : isles.dat abyss.dat last.dat

%.dat : books/%.txt COUNT_SRC
    $(COUNT_EXE) $< $*.dat

.PHONY : clean
clean :
        rm -f *.dat
        rm -f analysis.tar.gz
~~~

## Lesson 08-variables

> ## Extend the Makefile to create PNGs {.challenge}
>
> Add new rules, update existing rules, and add new macros to:
> 
> * Create `.png` files from `.dat` files using `plotcount.py`.
> * Add the script and `.png` files to the archive.
> * Remove all auto-generated files (`.dat`, `.png`,
>   `analysis.tar.gz`). 
> Finally, many Makefiles define a default [phony target](reference.html#phony-target) called `all` that will build what the Makefile has been written to build (e.g. in our case, `analysis.tar.gz`). As others may assume your Makefile confirms to convention and supports an `all` target, add an `all` target to your Makefile (Hint: this rule has a single dependency but no actions).

~~~{.bash}
# Makefile
# Count words script.
COUNT_SRC=wordcount.py
COUNT_EXE=python $(COUNT_SRC)

# Plot word counts script.
PLOT_SRC=plotcount.py
PLOT_EXE=python $(PLOT_SRC)


TXT_FILES=$(wildcard books/*.txt)
DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
PNG_FILES=$(patsubst books/%.txt, %.png, $(TXT_FILES))

.PHONY : all
all : analysis.tar.gz

# Generate archive file.
analysis.tar.gz : $(DAT_FILES) $(PNG_FILES) $(COUNT_SRC) $(PLOT_SRC)
    tar -czf $@ $^

# Count words.
.PHONY : dats
dats : $(DAT_FILES)

%.dat : books/%.txt $(COUNT_SRC)
    $(COUNT_EXE) $< $*.dat

# Plot word counts.
.PHONY : pngs
pngs : $(PNG_FILES)

%.png : %.dat $(PLOT_SRC)
    $(PLOT_EXE) $*.dat $*.png

.PHONY : clean
clean :
    rm -f $(DAT_FILES)
    rm -f $(PNG_FILES)
    rm -f analysis.tar.gz

.PHONY : variables
variables:
    @echo TXT_FILES: $(TXT_FILES)
    @echo DAT_FILES: $(DAT_FILES)
    @echo PNG_FILES: $(PNG_FILES)
~~~
