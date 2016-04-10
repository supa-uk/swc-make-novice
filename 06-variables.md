---
layout: page
title: Automation and Make
subtitle: Variables
minutes: 0
---

> ## Learning Objectives {.objectives}
>
> * Use variables in a Makefile.
> * Assign values to variables.
> * Reference variables.
> * Explain the benefits of decoupling configuration from 
>   computation.

Despite our efforts, our Makefile still has repeated content, namely
the name of our script, `wordcount.py`. If we renamed our script we'd
have to update our Makefile in multiple places.

We can introduce a Make [variable](reference.html#variable) (called a
[macro](reference.html#macro) in some versions of Make) to hold our
script name:

~~~ {.make}
COUNT_SRC=wordcount.py
~~~

This is a variable [assignment](reference.html#assignment) -
`COUNT_SRC` is assigned the value `wordcount.py`.

`wordcount.py` is our script and it is invoked by passing it to
`python`. We can introduce another variable to represent this
execution:

~~~ {.make}
COUNT_EXE=python $(COUNT_SRC)
~~~

`$(...)` tells Make to replace a variable with its value when Make
is run. This is a variable [reference](reference.html#reference). At 
any place where we want to use the value of a variable we have to
write it, or reference it, in this way.

Here we reference the variable `COUNT_SRC`. This tells Make to 
replace the variable `COUNT_SRC` with its value `wordcount.py`. When
Make is run it will assign to `COUNT_EXE` the value `python
wordcount.py`.

Defining the variable `COUNT_EXE` in this way allows us to easily
change how our script is run (if, for example, we changed the language
used to implement our script from Python to R).

> ## Use variables {.challenge}
>
> Update `Makefile` so that the `%.dat` and `analysis.tar.gz` rules
> reference the variables `COUNT_SRC` and `COUNT_EXE`.

We place variables at the top of a Makefile so that they are easy to
find and modify.

We can re-run Make to see that everything still works:

~~~ {.bash}
$ make clean
$ make dats
$ make analysis.tar.gz
~~~
