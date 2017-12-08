# Using create-react-app
**intro**

## Initializing Apps with create-react-app
- This is the beauty of it, after installing it to the global npm node_module's, just use the command below:
  - `create-react-app REACT_APP_NAME`
  - This bootstraps some great default starting modules, configs, HTML, CSS, JS, and webpack configs
- **NOTE** for some stupid reason ALE, expectes the `.eslintrc` to be called just that, no `*.json` or `*.js`
- **NOTE** the airbnb config requires jsx file extensions, but it can't be added to the babel config, add an exception to airbnb to avoid having to eject from the framework
  - Below is a suggesteed [exception][04] to fix the above problem from Stack Overflow

```json
"rules": {
  "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
}
```

## Using CRA with SASS/SCSS
- CRA tries not to pick too many favorites with its configs or doesn't want to configure all of the newer stylesheet frameworks and preprocessors, so SASS and SCSS needs to be added after the fact
- SASS is a ruby gem, so ruby needs to be on the host OS, which it typically is by default on most Linux distros and on macOS
- To install with ruby, just type: `gem install sass` and it should 
- **TODO** use [this][05] procedure instead even though the hackernoon [site][06] seems to document similar

## References
[01]: https://github.com/facebookincubator/create-react-app "Github: create-react-app"
[02]: https://www.fullstackreact.com/articles/using-create-react-app-with-a-server/ "FullstackReact: Using create-react-app"
[03]: https://manavsehgal.com/eslint-install-and-configure-for-react-apps-f7c3a28c5573 "manav sehgal:  eslint"
[04]: https://stackoverflow.com/questions/43031126/jsx-nor-allowed-in-files-with-extension-js-with-eslint-config-airbnb "Stack overflow: Adding jsx file extension exception in eslintrc"
[05]: https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#adding-a-css-preprocessor-sass-less-etc "CRA Docs: Adding CSS Preprocessor"
[06]: https://hackernoon.com/using-sass-with-create-react-app-without-ejecting-b5f4f827ed9e "Using Sass with Create React App (Without Ejecting)"

1. [Github: create-react-app][01]
2. [FullstackReact: Using create-react-app][02]
3. [Manav Seghal: ESlint][03]
4. [Stack Overflow: JSX file extension exception from eslintrc][04]
5. [CRA Docs: Adding CSS Preprocessor][05]
6. [Hackernoon: Using Sass with Create React App (Without Ejecting) by Jeff Duke][06]
