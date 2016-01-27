---
layout: page
title: More Version Control with Git
subtitle: Bisect
minutes: 10
---
> ## Learning Objectives {.objectives}
>
> * Understand how bisect works.

Let explore the repository:

~~~ {.shell}
$ ls
~~~
~~~ {.output}
en.dic
~~~
~~~ {.shell}
$ head en.dic
~~~
~~~ {.output}
AA
AAA
Aachen
aah
Aaliyah
aardvark
Aaron
ab
AB
ABA
~~~

`en.dic` is a file with English words.
At some point the word `Python` was added
but it doesn't exist in the last version.
**We want to find the commit that remove `Python`**
and the fast way to do it is using Git's `bisect` command.

We start using `bisect` with

~~~ {.bash}
$ git bisect start
~~~

Next we need to a good and bad commit.
For that bad commit we can use `master`.

~~~ {.bash}
$ git bisect bad master
~~~

> ## Find the good commit {.challenge}
>
> Look at the commit messages and find a good commit,
> i.e. a commit that has the word `Python`.

For the good commit we need to look at the Git history tree.

~~~ {.bash}
$ git log --all --decorate --graph --oneline
~~~
~~~ {.output}
* 3f3c1bf (HEAD -> master, origin/master, origin/HEAD) Add words
* 22dcdb5 Add words
*   92808ea Add words
|\  
| * 5121eef Add words
* |   ee40506 Add words
|\ \  
| |/  
|/|   
| * 51bd45c Add words
| * ee87332 Add words
* | 87db327 Add words
|/  
* cde9009 Add Python \o/
* 23c6a6b Add words
* 6318870 Begin
~~~

So we can use `cde9009`.

~~~ {.bash}
$ git bisect good cde9009
~~~
~~~ {.output}
Bisecting: 3 revisions left to test after this (roughly 2 steps)
[ee405062c7604cfd7f5b6be865794099043c5d54] Add words
~~~

When we provide the good and bad commit,
Git will checkout to the commit at the middle of the history
between the two commits that we inform.
We need to verify if the current commit is a good or bad commit
and give this information to Git.

~~~ {.bash}
$ grep Python en.dic
~~~

Since it is a bad commit we run

~~~ {.bash}
$ git bisect bad
~~~
~~~ {.output}
Bisecting: 1 revision left to test after this (roughly 1 step)
[51bd45c81c6bb33d759337051e51a1b716389ab2] Add words
~~~

And we repeat the same step until Git provide us with the first bad commit.

~~~ {.bash}
$ grep Python en.dic
~~~
~~~ {.bash}
$ git bisect bad
~~~
~~~ {.output}
Bisecting: 0 revisions left to test after this (roughly 0 steps)
[ee87332cbab454d7a97b04f9105eaa7e0767d527] Add words
~~~
~~~ {.bash}
$ grep Python en.dic
~~~
~~~ {.output}
Python
~~~
~~~ {.bash}
$ git bisect good
~~~
~~~ {.output}
51bd45c81c6bb33d759337051e51a1b716389ab2 is the first bad commit
commit 51bd45c81c6bb33d759337051e51a1b716389ab2
Author: Raniere Silva <raniere@rgaiacs.com>
Date:   Sun Dec 20 20:45:27 2015 -0200

    Add words

:100644 100644 1489061c7a6a606ef8ca16e7004f008c1c93a1cb 4996cc818c834f70271c898a40146e8e3a644084 M        en.dic
~~~