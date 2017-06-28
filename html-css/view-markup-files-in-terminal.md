# Viewing Marked Up Documents in Terminal
I stumbled across [this][md-view-snippet] nifty trick while researching [pandoc][pandoc-github]. It includes using [pandoc][pandoc-github] & [lynx][lynx-github] ( a text-based terminal browser) to first convert create a `stdout` text stream of the marked up file using [pandoc][pandoc-github] and then piping it into [lynx][lynx-github].

For now I'm mostly just using it in the form of a function within my [.bashrc][bashrc], and the way that looks is this:

```sh
function view-markup()
{
  pandoc $1 | lynx -stdin
}
```

This has been great, because as an effort to simultaneously improve my notetaking and information retaining skills, I've been writing my notes in markdown format since it's terminal friendly-ish and can be easily viewed in github or turned into html or other markdown, thus making it more obvious to strangers, friends or coworkers what I know, what I'm learning, and that I can express my thoughts effectively. Hence my hearing of [pandoc][pandoc-github]. Now I can just use my alias that takes me to my notes folder, use find if I can't remember the exact name and directory structure it resides in, and type in this command to quickly view it.

For future reference I'll try to test it with as many inputs and outputs as possible, which I'll need to do anyways to use [pandoc][pandoc-github]. I'll also probably want to have some kind of check and usage subfucntions to validate and make the function more user firendly and available to more people should they want it. I may also want some kind of integration with vim, ranger, and/or some kind of python to make inputing text patterns to find the file I'd like to view easier.

## To-Do's
- [ ] Validate Input
- [ ] Try other terminal markup viewers
- [ ] Make more complex python or bash script to go with it
  - [ ] GUI output options
  - [ ] Vim integration?
  - [ ] Ranger integration
  - [ ] Browser Integration
  - [ ] Input path pattern guesser or lookup cli options
- [ ] Test other pandoc output options for use with markup viewers

## References
[pandoc-github]: https://github.com/jgm/pandoc "Github repo for pandoc"
[lynx-github]: https://github.com/lynx/lynx "Github repo for lynx"
[md-view-snippet]: https://github.com/jgm/pandoc "Great snippet using pandoc & lynx to view docs in terminal"
[bashrc]: https://github.com/marcus-grant/mybash "My .bashrc configuration file repo"

