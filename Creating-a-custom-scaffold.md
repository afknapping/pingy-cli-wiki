> From `v0.10`, Pingy CLI now includes support for custom scaffolds. This page will cover the basics of creating a custom scaffold.

## `pingy-scaffold.json`

In a pingy scaffold only one file is mandatory, `pingy-scaffold.json`.
The file may have the following keys:

* name (mandatory)
* description (mandatory)
* dependencies
* devDependencies
* pingyJson
* files => input output vars

### `dependencies`

Packages that are required by your scaffolded websites in production. The user will be prompted to install these files while scaffolding their website. Follows the same format as specified in a [`package.json`](https://docs.npmjs.com/files/package.json#dependencies) file.

```json
"dependencies": {
    "bootstrap": "^4.0.0-beta",
    "jquery": "^3.2.1",
    "popper.js": "^1.12.5"
}
```

### `devDependencies`

Packages that are required by your scaffolded websites in development. The user will be prompted to install these files while scaffolding their website. Follows the same format as specified in a [`package.json`](https://docs.npmjs.com/files/package.json#dependencies) file

**Note: You should not include `@pingy/cli` in your `devDependencies`. Pingy CLI will automatically include this while scaffolding your site.**

```json
"devDependencies": {
    "node-sass": "^4.5.3"
},
```

⚠️ you need to add the modules to transform your source files as devDependencies. If you are using `.pug` files in your scaffold, you would need to add `pug`.


### `pingyJson`

Properties in `pingyJson` will be used to extend the scaffolded `pingy.json` file. You may include the following properties:

* `sourceMap` (Boolean). Default: `true`. Should Pingy use [Source Maps](https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/)?
* `minify` (Boolean). Default: `true`. Should Pingy minify? (only applies to exported files)
* `compile` (Boolean). Default: `true`. Should Pingy compile/transpile files (e.g.. Sass, Babel, Pug, etc..)?
* `exportDir` (String). Default: `dist`. The relative folder that Pingy should export your site to.
* `exclusions` (Array of Objects). Default: `[]`. Exclude files/folder from being exported or compiled.
 [Read more here](https://github.com/pingyhq/pingy-cli#configuring-export).
* `port` (number). Default: random available port. You should probably omit this property as the chosen port may not be available. It's usually best to let Pingy CLI choose an available port itself.

You do not need to include a property unless you wish to override the default. You do not need to include a `name` key as this is calculated based on the folder name that the site is being scaffolded into.

```
"pingyJson": {
  "sourceMap": false,
  "exclusions": [
    {
      "path": "node_modules",
      "action": "dontCompile",
      "type": "dir"
    },
    {
      "path": "node_modules/**",
      "action": "exclude",
      "type": "dir"
    },
    {
      "path": "!node_modules/{popper.js,popper.js/dist,popper.js/dist/**}",
      "action": "exclude",
      "type": "dir"
    }
  ]
},
```

### `files`

To include files in your scaffolded they must be included in the `files` array. The `files` array must have the following 2 properties:

* `input`. The path to the file to be copied from (relative to `pingy-scaffold.json`).
* `output`. The path to copy the file to (relative to current working directory (`process.cwd()`)). Indermediate directories will be created if they don't already exist.

```json
"files": [
  {
    "input": "index.html",
    "output": "index.html"
  },
  {
    "input": "styles/main.scss",
    "output": "styles/main.scss"
  }
]
```

⚠️ you need to add the modules for your file types as devDependencies in the `pingy-scaffold.json`. In the above example, you would need to add `node-sass`.

Optionally you may also include a `vars` object. If this is included then the input file will become a template and you may use the `vars` in the input template using double-curly braces (`{{ foo }}`) for interpolation. Templating uses [Markup.js](https://github.com/adammark/Markup.js/).

```
"files": [
  {
    "input": "scripts/main.js",
    "output": "scripts/main.js",
    "vars": {
      "foo": "world"
    }
  }
]
```

Given the above `files` example: If the input file is `alert('Hello {{ foo }}')` then the output will become `alert('Hello World')`.

## The scaffold command

There are three different path/url types that you can use to scaffold a new website. They are:

### `pingy scaffold <git url>`

The git repo can be hosted anywhere, it doesn't need to be on GitHub. This is a valid git URL: `https://github.com/pingyhq/pingy-scaffold-bootstrap-jumbotron.git`. This is **not** a valid git URL: `https://github.com/pingyhq/pingy-scaffold-bootstrap-jumbotron` (you can use the shorthand below instead).

### `pingy scaffold <shorthand github url>`

A shorthand GitHub URL can be in the form of `user/repo` or `user/(pingy-scaffold-)`. So, you can use `pingyhq/pingy-scaffold-bootstrap-jumbotron` or if your repo begins with the prefix `pingy-scaffold-`, then you can reference it like so `pingyhq/bootstrap-jumbotron`. Both will resolve to `https://github.com/pingyhq/pingy-scaffold-bootstrap-jumbotron.git`.

### `pingy scaffold <absolute local path>`

An absolute path on the local filesystem. Example: `/Users/dave/code/pingy-scaffolds/bootstrap-jumbotron`.

### What happens when you scaffold from a git repo?

Pingy will clone the repo to `~/.pingy/scaffolds` when it is first used. If you use the same scaffold again it will pull the repo to make sure that you are always scaffolding using the latest version of the repo.

## Example:

Take a look at the example repo for `bootstrap-jumbotron`: https://github.com/pingyhq/pingy-scaffold-bootstrap-jumbotron.

You may also scaffold a site from the example repo using `pingy scaffold pingyhq/bootstrap-scaffold`.

![pingy scaffold pingyhq/bootstrap-jumbotron](https://i.imgur.com/omLMEU1.gif)
