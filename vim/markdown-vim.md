# Markdown Editing In Vim



# Notes
- By default in `vim-markdown` **FIND config line & put here** is on **[verify]**
  - Highlights wrong spellings
  - jump to misspelling with `[` or `]` + `s` (think **s**pell (forward & back)
  - in normal mode once over a misspelled word, add to dictionary using `zg`
  - also mark correct words incorrect using `zw`
  - get a list of spelling suggestions on misspelled words from normal mode using `z=`

- Proper Detection of the `*.md` extension
    ```
    autocmd BufRead, BufNewFile *.md setifiletype markdown
    ```
    - You could also use this below line to call a special function enabling markdown specific functionality
    ```
    autocmd FileType markdown :call <SID>MarkdownSettingsFunc()
    ```

    - Useful to enable specific settings just for markdown
    - Disabling YCM might be nice here
    - Same for rulers, I don't think I like line rulers when editing prose

### Some Specifics on the markdown vim plugin
    - `leader` + `e` gives the block editor which works on both code and text blocks
      - Gives a focused window pane to edit text in good for prose and focus
    - `space` is unfortunataly for me remapped as a function binding used in the plugin to switch the current element within normal or selection mode to change
      - List items become check items and they become checked items, which go back to list items
      - Cycles through heading weights
    - `tab` & `shift` + `tab` become list indent/dedent
    - `]]` & `]]` jumps cursor to next/previous header


### References
[markdown-vim-ref]: https://github.com/gabrielelana/vim-markdown "vim-markdown readme @ github"
[vim-spell-guide]: https://www.linux.com/learn/using-spell-checking-vim "guide on vim spellcheck features"

# TODO's:
[ ] autocmd for detecting the `*.md` extension
[ ] folding
[ ] focused editing of selection or section
[ ] live preview module
[ ] focused prose mode
[ ] colorscheme that's nice for prose editing
[ ] equations
[ ] syntax highlighting? Probably not it's kind of distracting, toggle maybe?
[ ] word count
[ ] some of the hints from [spell tutorial][vim-spell-guide]
[ ] some ignore cases such as camel type words and underscored words
[ ] tabular for tables
[ ] jekyll for automatic blog publishing using markdown from cmd line
[ ] pandoc integrations to convert into whatever you want
