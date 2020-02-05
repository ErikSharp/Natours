# NPM notes

## Command examples

|Command|Notes|
|-|-|
|`npm install jquery -save`|Saves it as a dependency|
|`npm install node-sass -save--dev`|Saves it as a dev dependency (it will not get deployed)|
|`npm uninstall jquery -save`|Uninstalls and removes the entry from package.json|
|`npm install`|Installs all packages listed in package.json|
|`node -v`|States the Node.js version|
|`npm run erik`|Runs a script named erik|
|`npm start`|Runs the keyword start script (just shorthand so you don't have to write npm run start)|  
|`npm init`|Creates a `package.json` file|
|`npm init -y`|Creates a `package.json` file with all the fields defaulted|

## Getting scripts to run concurrently

https://itnext.io/4-solutions-to-run-multiple-node-js-or-npm-commands-simultaneously-9edaa6215a93

package.json
``` JSON
{
    "scripts": {       
        "start": "concurrently \"npm run compile:sass\" \"npm run live-server\"",
        "live-server": "live-server --open=./src/index.html --watch=src/index.html,src/css/style.css",
        "compile:sass": "node-sass ./src/sass/main.scss ./src/css/style.css -w"
    },
    "devDependencies": {
        "concurrently": "^5.1.0",
        "live-server": "^1.2.1",
        "node-sass": "^4.13.1"
    }
}

```