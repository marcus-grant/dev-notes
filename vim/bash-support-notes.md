# Vim as a BASH IDE Using the Bash-Support Vim Script

Loathe as I am to admit it because BASH can be a horrendous experience even for the more experienced people managing web applications or even their home workstations, but BASH certainly has its uses even in the face of Python, Perl, & more recently Go. This is because it has more direct access to whatever Unix-based kernel you might use it on. For example, though Python can be great to automate DevOps tasks, you can't as easily pipe functions together, concatenate output, and it's guaranteed to be present on any UNIX-based system after install, though Python is getting much more common on most distros. So unfortunately, shell scripts using shells like BASH are here to stay for the moment.

One way I deal with arduous shell scripting is by using my [custom-tailored Vim installation][vim-dotfile], which uses a vim-script called [bash-support][bash-support-ref]. It gives many extra features to vim in regards to shell scripting that are going to be covered here.

### Install

First install the thing. [This][bash-support-ref] is the repo for the script, where you can get the scripts required to make it work. I won't cover the other ways to install, I'll just cover the Vundle way. Open up your vim config file, usually `~/.vimrc` and towards the beginning where all the plugins are listed for Vundle to download and maintain, add the line `Plugin 'vim-scripts/bash-support.vim'` along with any other Plugins you want Vundle to install. Once the configuration file is saved, enter command mode in vim, and enter: `PluginInstall`, which should bring up Vundle's plugin manager view as it downloads and integrates the bash-support script into the vim configuration. When that is done, return to the vim configuration and add the line `filetype plugin on` to enable the script whenever the filetype of an opened file in vim is detected as a shell script. Once that is done, quit vim, find somewhere to write some test scripts, open vim to make a test script, and keep following along.

### Automatic Headers

The next logical topic to cover is how to create automatic shell script file headers to provide metadata for the script, and to describe it to anyone else that might use it. It is very likely that you might need the description portion of it in the future as well to remind yourself of its use. This is rather pleasantly done for you, along with the first `#!/bin/bash` line once a new shell script file is opened, usually by using the file extension, `some-script.sh`. If all is working, a header like this should be automatically entered for you in the new script.

```
#!/bin/bash
#============================================================
#
#          FILE:  some-script.sh
#
#         USAGE:  ./some-script.sh
#
#   DESCRIPTION:
#
#       OPTIONS:  ---
#  REQUIREMENTS:  ---
#          BUGS:  ---
#         NOTES:  ---
#        AUTHOR:   (),
#       COMPANY:
#       VERSION:  1.0
#       CREATED:  06/22/17 18:36 UTC
#      REVISION:  ---
#============================================================

```

By itself, this is pretty great. No need to do any of the annoying, or terse headers normally involved in a shell script. But even more can be done to make this even more automatic. By entering the below lines into your `.vimrc` any of the header text sections can be given automatic values. For me, this typically involves the `BASH_AuthorName`, `BASH_Email` and `BASH_Company`.


```
let g:BASH_AuthorName   = 'John Doe'
let g:BASH_Email        = 'john.doe@loremipsum.com'
let g:BASH_Company      = 'Lorem Ipsum Inc.'
```

Which should result in this instead:


```
#!/bin/bash
#============================================================
#
#          FILE:  some-script.sh
#
#         USAGE:  ./some-script.sh
#
#   DESCRIPTION:
#
#       OPTIONS:  ---
#  REQUIREMENTS:  ---
#          BUGS:  ---
#         NOTES:  ---
#        AUTHOR:  John Doe (), john.doe@loremipsum.com
#       COMPANY:  Lorem Ipsum Inc.
#       VERSION:  1.0
#       CREATED:  06/22/17 15:42:08 IST
#      REVISION:  ---
#============================================================

```

I personally have avoided a lot of nice comments like this because of how arduous they can be on an arduous syntax, but that only means that it's that much harder to figure out what's going on when I revisit a script a month later, or even worse if someone else needs to. Now, because the effort is reduced so much, they occure on most of my newer shell scripts.


### Functions & Function Headers

Now that new scripts are quickly given a header with useful information, now it'd be nice if smaller headers could be made for functions to describe their purpose and to make the script more readable. But first, what if vim could even automatically create a function, if given it's name? In normal mode, enter: `\sfu`, and it will give you a properly formatted, but barebones function. Then if still in normal mode and `\cfu` is entered, that same function will now have a nicely commented headerto be filled out with descriptions, parameters, and returns. Notice that because the `\sfu` was entered before `\cfu` near the function, the function name is filled in automatically.

```
#---  FUNCTION  ----------------------------------------------------------------
#          NAME:  main
#   DESCRIPTION:  Main function to encapsulate execution order while allowing
#               forward declarations of functions for readability
#    PARAMETERS:  All script parameters
#       RETURNS:  Exit code
#-------------------------------------------------------------------------------

main ()
{

}   # ----------  end of function main  ----------
```


### Framed Comments


```
#-------------------------------------------------------------------------------
# 
#-------------------------------------------------------------------------------
```

A framed comment like above can be useful to divide code into easily visible sections. Without a way to easily generate or copy these comments into the editor these can be too tedious to use with any regularity. Fortunately all that's needed with bash-support.vim is to enter `\cfr` while in *normal mode*.  **Note** that there is a pattern to the statements being entered here. Whenever the first letter following the backslash is a **c** it is used for a comment. The function, however, used an **s**. As can be seen later just about anything that inserts shell syntax as **s**nippets, use the letter **s**. Keep that in mind to have an easier time recalling bash-support statements.


### More Automatic Snippets

Having mentionsed that the '**s**' in bash-support statements following the backslash stands for **s**nippets, or at least might as well be, here are a few more that are quite useful. As they're read, attempt to figure out some mnemotic for them, or save and print out a [cheat sheet][cheat-sheet] to refer to.

- `\sl` **s**nippet for e**l**if then 
- `\si` **s**nippet for **i**f then fi statement
- `\sie` **s**nippet for **i**f then else fi statement
- `\sc` **s**nippet for a **c**ase in `esac` (the bash switch statement)
- `\sfo` **s**nippet for a **f**or ((...)) do done statement
- `\sw` **s**nippet for a **w**hile do done statement
- `\ss` **s**nippet for a **s**elect in the do done statement
- `\st` **s**nippet for a un**t**ill done statement
- `\se` **s**nippet for a **e**cho statement
- `\sp` **s**nippet for a **p**rintf statement

Also not that most snippet statements will automatically place the curst, in insert mode in logical places after pasting in the snippet.


### Reading & Writting Custom Snippets

Many have boilerplate code that need to be used more often than others during their time as developers or engineers. For those cases there are the `\nr` & `\nw` statements for **r**eading and **w**riting snippets respectively.

Code snippets can be written in to the plugin's path in `/your/vim/plugins/path/bash-support/codesnippets`. So there are two options to create new snippets. First, the `\nw` statement, which will prompt for all the information needed. Secondly, by creating a new file within the `codesnippets` path and giving its contents the exact textual information needed for the snippet, and giving the file the name to recall it with. For example, this is a custom snippet that gets used a lot:

```


```
 

## References
[bash-support-ref]: https://github.com/WolfgangMehner/vim-plugins/tree/master/bash-support "Vim Scripts Repository on Github with reference guide to bash-support"
[tutorial1]: http://www.thegeekstuff.com/2009/02/make-vim-as-your-bash-ide-using-bash-support-plugin/ "thegeekstuff.com: Tutorial on using BASH IDE in Vim"
[tutorial2]: https://www.tecmint.com/use-vim-as-bash-ide-using-bash-support-in-linux/ "Tecmint: Vim as Bash IDE using bash-support"
[vim-repo]: https://github.com/marcus-grant/vimdependant "My custom-made vim dotfiles and configs"
[cheat-sheet]: some-site.com "Cheat sheet for bash-support statements"

## To-Do's
- [ ] Add link to your vim config
- [ ] Find or make a cheat sheet to bash-support statements to link to
