# Javascript development environment

## Editor config
- Atom for Mac: install editorconfig plugin by sidresorhus from Atom > Preferences > Install
- paste this in:
```
root = true

[*]
indent_style = tab
indent_size = 4
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false
```

___Packages and tools used:___
- editorconfig.org

## Node + NPM
- the package.json can be viewed here https://github.com/coryhouse/javascript-development-environment/blob/master/package.json
- install globally the node security project `npm install -g nsp`

___Packages and tools used:___
- node security project or `nsp`. It can be run with `nsp check`

## Development web server
- create files and folders at root of the project:
```
/buildScripts/srcServer.js
/src/index.html
```
- create a simple `express` server in the `srcServer.js` file that will serve the index.html file in the `src` folder

```javascript
var express = require( "express" );
var path = require( "path" );
var open = require( "open" );

var port = 3000;
var app = express();

app.get( "/", function( req, res ) {
	res.sendFile( path.join( __dirname, "../src/index.html" ) );
} );

app.listen( port, function( err ) {
	if ( err ) {
		console.log( err );
	} else {
		open( "http://localhost:" + port );
	}
} );
```

- run the server `node /buildScripts/srcServer.js`

___Packages and tools used:___
- `npm install --save-dev open` - https://www.npmjs.com/package/open

## Work in progress sharing
- available tools and services: [localtunnel](https://localtunnel.github.io/www/), [ngrok](https://ngrok.com/), [surge](https://surge.sh/), [now](https://zeit.co/now)

#### Localtunnel setup:
- `npm install localtunnel -g`
- start app
- share work by exposing the local port: `lt --port 3000`
- you can also add a subdomain `lt --port --subdomain something here 3000`
- creates a local web server that can be shared

#### NGROK setup:
- signup
- install ngrok
- install authtoken
- start local app
- ./ngrok http and the port that your app is running on
- creates a local web server that can be shared but it's password protected

#### Now
- `npm install -g now`
- create start script
- `now`
- moves the application to the cloud

#### Surge
- only supports static files. moves the static files to their service
- `npm install -g surge`
- `surge`

## Automating work with npm
- add a command to the package.json
```json
"scripts": {
	"start": "node ./buildScripts/srcServer.js",
	"test": "echo \"Error: no test specified\" && exit 1"
},
```
- command that will log a message to the console in a different color
```json
"scripts": {
	"prestart": "node ./buildScripts/startMessage.js",
	"start": "node ./buildScripts/srcServer.js",
	"test": "echo \"Error: no test specified\" && exit 1"
},
```

- command that will share work with localtunnel
```json
"scripts": {
	"prestart": "node ./buildScripts/startMessage.js",
	"start": "node ./buildScripts/srcServer.js",
	"share": "lt --p 3000",
	"test": "echo \"Error: no test specified\" && exit 1"
},
```

- using `chalk` to add colour to console log messages
```javascript
var chalk = require( "chalk" );
console.log( chalk.green( "Starting app in dev mode..." ) );
```

- `npm-run-all` to run scripts in parallel

___Packages and tools used:___
- npm scripts hook: `prestart`
- chalk: `npm install chalk`
- npm-run-all: `npm install npm-run-all`

## Transpiling
- install packages for babel: `npm install --save-dev babel-cli babel-core babel-preset-env babel-register`
- create `.babelrc` config file:
```json
{
  "presets": ["env"]
}
```
- change build scripts to use `babel-node`
```json
{
	"prestart": "babel-node ./buildScripts/startMessage.js",
	"start": "babel-node ./buildScripts/srcServer.js",
}
```

## Bundling
- create file `webpack.config.dev.js`
- copy/paste the contents from [github](https://gist.github.com/coryhouse/d611e83e432f3ae65cc46ebb9b599930)
- remove the following parameters from the file: `debug`, `noInfo`;
- it is no longer possible to specify a loader without the `-loader` suffix. Change

```javascript
module: {
    loaders: [
      {test: /\.js$/, exclude: /node_modules/, loaders: ['babel']},
      {test: /\.css$/, loaders: ['style','css']}
    ]
  }
```

to

```javascript
module: {
    loaders: [
      {test: /\.js$/, exclude: /node_modules/, loaders: ['babel-loader']},
      {test: /\.css$/, loaders: ['style-loader','css']}
    ]
  }
```
