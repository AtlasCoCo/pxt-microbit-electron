![Interface](Interface.png)

# PXT micro:bit Electron app

A prototype for an offline (not quite there yet) standalone client of PXT.

This is an electron wrapper around the web app.

## Installation
```
git clone https://github.com/Microsoft/pxt-microbit-electron.git
```

If you want to link your local repos:
```
npm link ../pxt
npm link ../pxt-microbit
```

Finally:
```
npm install
```

Please see the Error section below if you get errors during installation.

To remove links to your local repos, uninstall them from node_modules and reinstall them from NPM:
```
npm uninstall pxt-core
npm uninstall pxt-microbit
npm install
```

## Launching the Electron app
```
npm start
```

Please see the Error section below if you get errors while running the app (such as weird DLL errors).

## Errors while installing or running
If your Node version differs from Electron's Node version, you will need to rebuild some native modules in the dependencies of the web app.

### Requirements
- [Python 2.x](https://www.python.org/downloads/) (__NOT 3.x__); make sure `python` is in your global Path
- (Windows only) Visual Studio; any of the newer versions will do (Community 2015 works fine)

### Rebuilding the native modules
Once you have the above dependencies:
```
npm run rebuild-native
```

### Still have errors?
If you still can't run the Electron app even after rebuilding the native modules, you can try the following:
```
npm uninstall electron
npm uninstall electron-rebuild
npm install
npm run rebuild-native
```

### `pxt serve` errors
Rebuilding the native modules can break `pxt serve` outside of the Electron app. To fix, uninstall the native modules from your PXT repo, and reinstall them:

```
npm uninstall serialport
npm uninstall ...
npm install
```

## Packaging the app for release
Run the following:

```
npm run package
```

### Note on npm link
If you've linked `pxt-core` and `pxt-microbit`, packaging won't work (electron-packager seems to support only 1 level of symlink, whereas `npm link` generates 2 symlinks). You will need to uninstall your linked repos and reinstall them via NPM:
```
npm uninstall pxt-core
npm uninstall pxt-microbit
npm install
```

If you need to use a non-released version of `pxt-core` or `pxt-microbit`, you can still do it without `npm link` by installing them from their cloned repo. Make sure their repos are checked out at the branch you wish to package in the Electron app, and then run:
```
npm install ../pxt
npm install ../pxt-microbit
```

Remember to run `npm run rebuild-native` before packaging, and make sure the app is working by running `npm start`.

## Adding a dependency that is or contains a native modules
Whenever you add a dependency to package.json, there is a chance that it (or one of its nested dependencies) has a native component. If that is the case, and the Electron app stops working even after you run `npm run rebuild-native`, you may need to modify our `rebuild-native.js` script.

`electron-rebuild` does not always succeed in cleaning up the native modules that it rebuilds. In our `dev_tools/rebuild-native.js` script, we maintain a list of known native modules that we manually clean up. You will need to figure out where the module keeps its built native components, and add an entry to the `knownNativeModules` array in the `rebuild-native.js` script.

For example, serialport keeps its built components under `build/release`, so we added the following entry:

```
let knownNativeModules = [
    {
        packageName: "serialport",
        cleanDir: path.join("build", "release")
    }
];
```
