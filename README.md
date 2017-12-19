Heroku buildpack: TeX
=====================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks)
for working with TeX documents. In its raw form, it simply bundles a working
TeX Live environment into your Heroku app and doesn't do anything else with it.


    $ ls

    $ heroku create --buildpack git://github.com/ThibaultLatrille/heroku-buildpack-tex.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom build pack... done
    -----> TeX app detected
    ...

This can be useful if you simply want to play around with TeX Live without
having to build or install it yourself. You can pull it up on your instance
easily in bash:

    $ heroku run bash

Multipacks
----------

More likely, you'll want to use it as part of a larger project, which needs to
build PDFs. The easiest way to do this is with a mutlipack.

    $ heroku buildpacks:add git://github.com/ThibaultLatrille/heroku-buildpack-tex.git

This will bundle TeX Live into your instance without impacting your existing
system. You can then call out to executables like `pdflatex` as you would on
any other machine.

Build the TexLive binary
----------

Create a new heroku app and connect to it

    $ heroku create buildpack-tex
    $ heroku run bash --app buildpack-tex

Download and extract TexLive

    $ wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz -O install-tl-unx.tar.gz
    $ tar -xvzf install-tl-unx.tar.gz

TexLive install 

    $ cd /app/install-tl-20171218
    $ perl ./install-tl
    
changes: 
TEXDIR /app/texlive
set installation scheme: scheme-basic
NO install macro/font doc tree
NO install macro/font source tree

Set PATH

    $ export PATH=/app/texlive/bin/x86_64-linux:$PATH

Install recommended packages

    $ tlmgr install ec lm eurosym babel-greek babel-french

test if compilation works

    $ wget https://controversciences.org/timelines_download_tex?timeline_id=159 -O input.tex
    $ pdflatex input.tex
    $ rm -rf ./input.*

Create binary archive

    $ cd /app/texlive
    $ tar czf /app/texlive.tar.gz -C /app/texlive .

Upload archive

    $ cd /app
    $ git init
    $ git add texlive.tar.gz
    $ git config --global user.name "Thibault Latrille"
    $ git config --global user.email "myemail@gmail.com"
    $ git commit -m "TexLive compressed"
    $ git push --set-upstream https://github.com/ThibaultLatrille/texlive.git master --force 
   
Don't forget to update your PATH var in heroku such that it includes /app/texlive/bin/x86_64-linux:
