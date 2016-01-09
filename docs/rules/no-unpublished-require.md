# Disallow `require()`s for files that files that are not published (no-unpublished-require)

If `require()` targets are not published, the program works in local, but will not work after published to npm.
This rule catches `require()` for files and modules that not published.

## Rule Details

This rule checks the file paths of `require()`s.
If the file paths are not published, this reports these.

"published" is that satisfying the following conditions:

- If it's a file:
  - `"files"` field of `package.json` includes the file, or the field is nothing.
  - `.npmignore` does not include the file.
- If it's a module:
  - `"dependencies"` or `"peerDependencies"` field of `package.json` includes the module.
    If the file `require` is written is not published then it's also OK that `"devDependencies"` field of `package.json` includes the module.

The following patterns are considered problems:

```js
/*eslint node/no-unpublished-require: 2*/

var ignoredFile = require("./ignored-file");             /*error "./ignored-file" is not published.*/
var notDependedModule = require("not-depended-module");  /*error "not-depended-module" is not published.*/
```

The following patterns are considered not problems:

```js
/*eslint node/no-unpublished-require: 2*/

var publishedFile = require("./published-file");
var dependedModule = require("depended-module");

// This rule cannot check for dynamic imports.
var foo = require(FOO_NAME);
```

### Options

```json
{
    "rules": {
        "node/no-unpublished-require": [2, {
            "tryExtensions": [".js", ".json", ".node"]
        }]
    }
}
```

#### `tryExtensions`

When an import path does not exist, this rule checks whether or not any of `path.js`, `path.json`, and `path.node` exists.
`tryExtensions` option is the extension list this rule uses at the time.

Default is `[".js", ".json", ".node"]`.

This option can be set by [shared settings](http://eslint.org/docs/user-guide/configuring.html#adding-shared-settings).
Several rules have this option, but we can set this option at once.

```json
{
    "settings": {
        "node": {
            "tryExtensions": [".js", ".json", ".node"]
        }
    },
    "rules": {
        "node/no-unpublished-require": 2
    }
}
```

## When Not To Use It

If you don't want to be notified about usage of `require()`, then it's safe to disable this rule.