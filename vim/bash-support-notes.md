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
#       CREATED:  02/14/09 15:42:08 IST
#      REVISION:  ---
#============================================================

```

By itself, this is pretty great. No need to do any of the annoying, or terse headers normally involved in a shell script. But even more can be done to make this even more automatic. By entering the below lines into your `.vimrc` any of the header text sections can be given automatic values. For me, this typically involves the `BASH_AuthorName`, `BASH_Email` and `BASH_Company`.


```
let g:BASH_AuthorName   = 'John Doe'
let g:BASH_Email        = 'john.doe@loremipsum.com'
let g:BASH_Company      = 'Lorem Ipsum Inc.'
```




## References
[bash-support-ref]: https://github.com/WolfgangMehner/vim-plugins/tree/master/bash-support "Vim Scripts Repository on Github with reference guide to bash-support"
[tutorial1]: http://www.thegeekstuff.com/2009/02/make-vim-as-your-bash-ide-using-bash-support-plugin/ "thegeekstuff.com: Tutorial on using BASH IDE in Vim"
[tutorial2]: https://www.tecmint.com/use-vim-as-bash-ide-using-bash-support-in-linux/ "Tecmint: Vim as Bash IDE using bash-support"
[vim-repo]: https://github.com/marcus-grant/vimdependant "My custom-made vim dotfiles and configs"

## To-Do's
- [ ] Add link to your vim config
