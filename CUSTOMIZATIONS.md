
## Fork the repo and make sure it works

```bash
yarn
yarn start
yarn test
```

## Add gitattributes to improve cross platform line endings

```bash
cp ../Auction-Web-Client/.gitattributes .
git add .gitattributes
git commit -m "add gitattributes file"
```

## eslint customization

Changed `.eslintrc` then:

```bash
yarn run lint:fix
yarn run lint
git commit -am "changed lint rules"
```

## set Project code style for WebStorm to match eslint

Fiddle with WebStorm JS Code style options, making sure to use "Project Scheme" until it formats code that
lint does not complain about.  This makes a file `.idea/codeStyleSettings.xml`

Update gitignore so we can commit this file.  Replace:

```
.idea/
```

with

```
.idea/**
.idea/**/
!.idea/codeStyleSettings.xml
```

Now commit:

```bash
git add .gitignore
git add .idea/
```

## Enable browser target configuration from `project.config.js`

Note: I've submitted a PR to the original project for these changes so hopefully we won't have to do them ourselves going forward.

First need to update babel-preset-env to 1.5.2:

```bash
yarn upgrade babel-preset-env --dev
```

Now add this section to `project.config.js`:

```
  /** hashmap of target environments the code must run in (see https://github.com/babel/babel-preset-env#targets */
  targetEnvironments: {
    chrome: "57",
    firefox: "50",
    edge: "14",
    safari: "10",
  },
```

Now modify the babel-presets section in `build/webpack.config.js`

```
      presets: [
        'babel-preset-react',
        ['babel-preset-env', {
          modules: false,
          targets: Object.assign({}, project.targetEnvironments, { uglify: true }),
        }],
      ]
```

## Expose CSS Modules option in project.config.js to make it easier to enable/disable

Add this to `project.config.js`:

```
 /** Whether to use CSS Modules in your scss files */
 scssModules: false,
```

And modify the sass loader section of `build/webpack.config.js`

```diff
 
// Styles
// ------------------------------------
const extractStyles = new ExtractTextPlugin({
  filename: 'styles/[name].[contenthash].css',
  allChunks: true,
+  disable: false,
});

config.module.rules.push({
 test: /\.(sass|scss)$/,
  loader: extractStyles.extract({
    fallback: 'style-loader',
    use: [
      {
        loader: 'css-loader',
        options: {
          sourceMap: project.sourcemaps,
+          modules: project.scssModules,
+          localIdentName: __DEV__ ? "[name]__[local]___[hash:base64:5]" : "[hase:base64]",
+          importLoaders: 1,
```

## Switch to React Router 4.x

At this point we start to deviate from the original starter in ways that make it hard to merge.

```bash
yarn remove react-router
yarn add react-router-dom react-router history
```

make a bunch of code changes
commit!
