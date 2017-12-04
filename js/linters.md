# Linting with Meteor

## Installing Linters
- Linters are installed using node or yarn
```
yarn add --dev eslint babel-eslint eslint-plugin-react eslint-plugin-jsx-a11y

npm install --save-dev eslint babel-eslint eslint-plugin-react eslint-plugin-jsx-a11y
```

## Neovim Install & Configuration
- ESLint sometimes seems to work better when there's a global install
  - `sudo npm i g --save-dev eslint babel-eslint eslint-plugin-react`
  - **ToDo** figure out why and in which cases eslint needs local installs vs global
- Using `vim-plug` this is how you get some decent configs for eslint
  - include `w0rp/ale` as a plugin
  - update vim plugins
  - add some configurations like these:
```
" enable filetypes using the associated linting program                       
let g:ale_fixers = {                                                          
\   'javascript': ['eslint'],                                                 
\}                                                                            
" lint on saves                                                               
let g:ale_fix_on_save = 1                                                     
" TODO: test with flow on non typescript                                      
" let g:ale_completion_enabled = 1                                            
let g:ale_sign_error = '●' " Less aggressive than the default '>>'            
let g:ale_sign_warning = '.'                                                  
let g:ale_lint_on_enter = 0 " Less distracting when opening a new file
```

## Local Linting Settings
- I still don't really know if local installs through npm/yarn are needed for the project to have linting work, **investigate**
- I prefer the airBnB config, so download that locally using `npx eslint --init`
  - This loads the eslint module from the local `node_module` and initializes an `eslintrc` file with the specifications you give the `eslint --init` script.
  - Here the option to create an `eslintrc` are presented as a set of prompts, simply choosing to use a popular style guide and then answering the additional questions for the `airbnb` config works best.
  - This avoid the more complicated way of installing the `eslint-config-airbnb` directly
- **NOTE** for some reason ALE requires the lint config file to be without a `.js` or `.json` extension, investigate why this is





## Meteor Complicates Linting
- Unfortunately meteor complicates linting a bit
- To get around this let's see what their [documentation][10] says
- There, they suggest the AirBnB lint [configuration][02]


## Installing & Running ESLint (Meteor)
- To setup ESLint for a meteor application, install the following npm packages:
  - `meteor npm install --save-dev babel-eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-meteor eslint-plugin-react eslint-plugin-jsx-a11y eslint-import-resolver-meteor eslint @meteorjs/eslint-config-meteor`
    - If using react, include this below:
    - `meteor npm install --save-dev eslint-plugin-react`

> Meteor comes with npm bundled so that you can type meteor npm without worrying about installing it yourself

- Then, to configure how the linter is run, add a `eslintConfig` section to `package.json`, to specify that the `eslint-config-airbnb` configuration gets used
  - This includes nice things like ES6 syntax and other modern style rules to ES6
- Below is how `package.json` ought be configured:
```json
{
  ...
  "scripts": {
    "lint": "eslint .",
    "pretest": "npm run lint --silent"
  },
  "eslintConfig": {
    "extends": "@meteorjs/eslint-config-meteor"
  }
}
```

- Then run the linter using: `meteor npm run lint`
- For more directions on how to configure ESLint, read their [Getting Started][03] documentation


## References
[01]: https://drivy.engineering/setting-up-vim-for-react/ "drivy engineering article on setting up neovim for linting"
[02]: https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb
[03]: http://eslint.org/docs/user-guide/getting-started "ESLint Getting Started"
[10]: https://guide.meteor.com/code-style.html#eslint "Meteor Docs: Checking code with ESLint"

1. [Drivy Engineering: Linting with NeoVim][01]

2. [AirBnB ESLint Configuration][02]
3. [ESLint Docs: Getting Started][03]
10. [Meteor Docs: Check Your Code with ESLint][10]
