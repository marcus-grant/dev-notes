# Linting with Meteor

## Meteor Complicates Linting
- Unfortunately meteor complicates linting a bit
- To get around this let's see what their [documentation][1] says
- There, they suggest the AirBnB lint [configuration][2]


## Installing & Running ESLint
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
- For more directions on how to configure ESLint, read their [Getting Started][3] documentation


## References
[1]: https://guide.meteor.com/code-style.html#eslint "Meteor Docs: Checking code with ESLint"
[2]: https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb
[3]: http://eslint.org/docs/user-guide/getting-started "ESLint Getting Started"

1. [Meteor Docs: Check Your Code with ESLint][1]
2. [AirBnB ESLint Configuration][2]
3. [ESLint Docs: Getting Started][3]
