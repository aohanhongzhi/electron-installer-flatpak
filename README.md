# electron-installer-flatpak [![Version](https://img.shields.io/npm/v/electron-installer-flatpak.svg)](https://www.npmjs.com/package/electron-installer-flatpak)

Create a flatpak for your Electron app. This is based off the
[electron-installer-debian](https://github.com/unindented/electron-installer-debian)
tool.

## Requirements

This tool requires `flatpak` >= 0.6.13 to be installed on your system.


## Installation

For use from command-line:

```
$ npm install -g electron-installer-flatpak
```

For use in npm scripts or programmatically:

```
$ npm install --save-dev electron-installer-flatpak
```


## Usage

Say your Electron app lives in `path/to/app`, and has a structure like this:

```
.
├── LICENSE
├── README.md
├── node_modules
│   ├── electron-packager
│   └── electron-prebuilt
├── package.json
├── resources
│   ├── Icon.png
│   ├── IconTemplate.png
│   └── IconTemplate@2x.png
└── src
    ├── index.js
    ├── main
    │   └── index.js
    └── renderer
        ├── index.html
        └── index.js
```

You now run `electron-packager` to build the app for flatpak:

```
$ electron-packager . app --platform linux --arch x64 --out dist/
```

And you end up with something like this in your `dist` folder:

```
.
└── dist
    └── app-linux-x64
        ├── LICENSE
        ├── LICENSES.chromium.html
        ├── content_shell.pak
        ├── app
        ├── icudtl.dat
        ├── libgcrypt.so.11
        ├── libnode.so
        ├── locales
        ├── natives_blob.bin
        ├── resources
        ├── snapshot_blob.bin
        └── version
```

How do you turn that into a flatpak package that your users can install?

### Command-Line

If you want to run `electron-installer-flatpak` straight from the command-line, install the package globally:

```
$ npm install -g electron-installer-flatpak
```

And point it to your built app:

```
$ electron-installer-flatpak --src dist/app-linux-x64/ --dest dist/installers/ --arch x86_64
```

You'll end up with the package at `dist/installers/app_0.0.1_x86_64.flatpak`.

### Scripts

If you want to run `electron-installer-flatpak` through npm, install the package locally:

```
$ npm install --save-dev electron-installer-flatpak
```

Edit the `scripts` section of your `package.json`:

```js
{
  "name": "app",
  "description": "An awesome app!",
  "version": "0.0.1",
  "scripts": {
    "start": "electron .",
    "build": "electron-packager . app --platform linux --arch x86_64 --out dist/",
    "flatpak64": "electron-installer-flatpak --src dist/app-linux-x64/ --dest dist/installers/ --arch x86_64"
  },
  "devDependencies": {
    "electron-installer-flatpak": "*",
    "electron-packager": "*",
    "electron-prebuilt": "*"
  }
}
```

And run the script:

```
$ npm run flatpak64
```

You'll end up with the package at `dist/installers/app_0.0.1_x86_64.flatpak`.

### Programmatically

Install the package locally:

```
$ npm install --save-dev electron-installer-flatpak
```

And write something like this:

```js
var installer = require('electron-installer-flatpak')

var options = {
  src: 'dist/app-linux-x64/',
  dest: 'dist/installers/',
  arch: 'amd64'
}

console.log('Creating package (this may take a while)')

installer(options, function (err) {
  if (err) {
    console.error(err, err.stack)
    process.exit(1)
  }

  console.log('Successfully created package at ' + options.dest)
})
```

You'll end up with the package at `dist/installers/app_0.0.1_x86_64.flatpak`.

### Options

Even though you can pass most of these options through the command-line interface, it may be easier to create a configuration file:

```js
{
  "dest": "dist/installers/",
  "icon": "resources/Icon.png",
  "categories": [
    "Utility"
  ]
}
```

And pass that instead with the `config` option:

```
$ electron-installer-flatpak --src dist/app-linux-x64/ --arch x86_64 --config config.json
```

Anyways, here's the full list of options:

#### src
Type: `String`
Default: `undefined`

Path to the folder that contains your built Electron application.

#### dest
Type: `String`
Default: `undefined`

Path to the folder that will contain your flatpak installer.

#### rename
Type: `Function`
Default: `function (dest, src) { return path.join(dest, src); }`

Function that renames all files generated by the task just before putting them in your `dest` folder.

#### options.id
Type: `String`
Default: `io.atom.electron`

App id of the flatpak, used in the [`id` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

#### options.productName
Type: `String`
Default: `package.productName || package.name`

Name of the application (e.g. `Atom`), used in the [`Name` field of the `desktop` specification](http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).

#### options.genericName
Type: `String`
Default: `package.genericName || package.productName || package.name`

Generic name of the application (e.g. `Text Editor`), used in the [`GenericName` field of the `desktop` specification](http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).

#### options.description
Type: `String`
Default: `package.description`

Short description of the application, used in the [`Comment` field of the `desktop` specification](http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).

#### options.branch
Type: `String`
Default: `master`

Release branch of the flatpak, used in the [`branch` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

#### options.base
Type: `String`
Default: `io.atom.electron.BaseApp`

Base app to use when building the flatpak, used in the [`base` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

#### options.baseVersion
Type: `String`
Default: `master`

Base app version, used in the [`base-version` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).


#### options.baseFlatpakref
Type: `String`
Default: `FIXME`

Url of a flatpakref to use to auto install the base application.

#### options.runtime
Type: `String`
Default: `org.freedesktop.Platform`

Runtime id, used in the [`runtime` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

#### options.runtimeVersion
Type: `String`
Default: `1.4`

Runtime version, used in the [`runtime-version` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

#### options.sdk
Type: `String`
Default: `org.freedesktop.Sdk`

Sdk id, used in the [`sdk` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

#### options.arch
Type: `String`
Default: `undefined`

Machine architecture the package is targeted to.

#### options.finishArgs
Type: `Array[String]`
Default:
```js
[
  // X Rendering
  '--socket=x11', '--share=ipc',
  // Open GL
  '--device=dri',
  // Audio output
  '--socket=pulseaudio',
  // Read/write home directory access
  '--filesystem=home',
  // Chromium uses a socket in tmp for its singleton check
  '--filesystem=/tmp',
  // Allow communication with network
  '--share=network',
  // System notifications with libnotify
  '--talk-name=org.freedesktop.Notifications'
],
```

Arguments to use when call `flatpak build-finish`, use in the [`finish-args` field of a flatpak-builder manifest](http://flatpak.org/flatpak/flatpak-docs.html#flatpak-builder).

Changing this can be used to customize permissions of the sandbox the flatpak will run in.

#### options.bin
Type: `String`
Default: `package.name`

Relative path to the executable that will act as binary for the application, used in the [`Exec` field of the `desktop` specification](http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).

The generated package will contain a symlink `/usr/bin/<%= options.name %>` pointing to the path provided here.

For example, providing this configuration:

```js
{
  src: '...',
  dest: '...',
  name: 'foo',
  bin: 'resources/cli/launcher.sh'
}
```

Will create a package with the following symlink:

```
usr/bin/foo@ -> ../lib/foo/resources/cli/launcher.sh
```

And a desktop specification with the following `Exec` key:

```
Exec=foo %U
```

#### options.icon
Type: `String` or `Object[String:String]`
Default: `undefined`

Path to a single image that will act as icon for the application:

```js
{
  icon: 'resources/Icon.png'
}
```

Or multiple images with their corresponding resolutions:

```js
{
  icon: {
    '48x48': 'resources/Icon48.png',
    '64x64': 'resources/Icon64.png',
    '128x128': 'resources/Icon128.png',
    '256x256': 'resources/Icon256.png'
  }
}
```

#### options.categories
Type: `Array[String]`
Default: `[]`

Categories in which the application should be shown in a menu, used in the [`Categories` field of the `desktop` specification](http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).

For possible values check out the [Desktop Menu Specification](http://standards.freedesktop.org/menu-spec/latest/apa.html).

#### options.mimeType
Type: `Array[String]`
Default: `[]`

MIME types the application is able to open, used in the [`MimeType` field of the `desktop` specification](http://standards.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html).


## Meta

* Code: `git clone git://github.com/endlessm/electron-installer-flatpak.git`
* Home: <https://github.com/endlessm/electron-installer-flatpak/>


## Contributors

* Daniel Perez Alvarez ([unindented@gmail.com](mailto:unindented@gmail.com))
* Matt Watson ([mattdangerw@gmail.com](mailto:mattdangerw@gmail.com))


## License

Copyright (c) 2016 Daniel Perez Alvarez ([unindented.org](https://unindented.org/)). This is free software, and may be redistributed under the terms specified in the LICENSE file.
