+++
title = "Home .git"
description = "Versioning the contents of your home directory"
tags = [
	"config",
	"git",
	"workflow"
]
date = "2019-06-09"
+++

Git is an incredibly useful tool for a programmer, you use it to version the work you do and distribute it easily on other computers.
But over the years, I've found that a lot of my workflow depends on various configurations and helper scripts I have in my path.
So why not version those as well, it does allow you to get started in seconds in a very familiar environment on every new computer or even a server if you spend a lot of your time in SSH sessions.
Bonus points for making it a public repository so others can take a look at your stuff and possibly get inspiration for improvements in their workflow, just make sure you don't commit any private keys.

# The setup

Feel free to check out my home repository over [here](https://github.com/tophatcroat/home). As you can see, it contains my `.zshrc`, `.vimrc` a `bin`directory with various scripts and some other stuff I've created while doing what I do best, fidgeting around a computer.

You can start your own version by simply initializing a Git repository in your home directory.
```sh
cd ~ && git init
```
Now your directory will be full of stuff you probably don't want to commit, like your downloads, pictures, private keys, shell history or what have you. So it's important to set up the `~/.gitignore` file immediately to stop you from accidentally committing stuff you didn't want to.
```sh
# Blacklist all in this folder
/*
# Except for these
!.gitignore
!.gitconfig
!.notes/
!bin/
!Development/Sh
```
This will instruct Git to keep its hands off all files except for the `.gitignore`, `.gitconfig` and the files in the `.notes`, `bin` and `Development/Sh` directories.
Adding any other file you will need will require a force flag like so `git add -f path_to_file`.

And that's basically all you need to do to set this up, it's now up to you to add all you want and push it to the Git hosting service of your choice.

# The cloning

So now it's time to clone this into your new home directory, but if you try cloning it using `git clone` command it will complain that it can't do that in an non empty directory.
To get around this, you need to initialize it first then manually add the remote and finally force a checkout like so:
```sh
cd ~
# Create an empty git repository in your home folder
git init
# Add the remote
git remote add origin https://github.com/TopHatCroat/home
# Get the stuff from up there
git fetch
# Be careful! This will overwrite any local files existing on remote!
git reset --hard origin/master
```
Running the last command will basically checkout all the files from the origin master branch and overwrite any files with the same path, so be careful if you already did some setup on the new machine.

And that's it, you've saved yourself the bother of copying over the config files.
The only thing that remains is to install the tools that use the configs you've just brought over.
If you are feeling adventurous you could make a script that will do that for you like I did in [here](https://github.com/TopHatCroat/home/blob/master/Development/Sh/setup.sh), but that is up to you.

