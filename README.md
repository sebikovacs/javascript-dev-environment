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
- run `npm install --save-dev style-loader css-loader`;
- it is no longer possible to specify a loader without the `-loader` suffix. Change
- using `debugger;` anywhere in the javascript files will halt the compilation of the file and the file will be inspectable due to the use of sourcemaps
- sourcemaps are used by adding `devtool: 'inline-source-map'` to the webpack file

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
      {test: /\.css$/, loaders: ['style-loader','css-loader']}
    ]
  }
```

___Packages and tools used:___
- packages: `css-loader`, `style-loader`

## Linting
- the code for the eslint can be found here [github gist](https://gist.github.com/coryhouse/61f866c7174220777899bcfff03dab7f)
- eslint can be installed globally with `npm install -g eslint`
- eslint can generate a configuration file with the command `eslint --init`
- this will prompt a few question based on which the configuration file will be created:
```json
{
  "root": true,
  "extends": [
    "eslint:recommended",
    "plugin:import/errors",
    "plugin:import/warnings"
  ],
  "parserOptions": {
    "ecmaVersion": 7,
    "sourceType": "module"
  },
  "env": {
    "browser": true,
    "node": true,
    "mocha": true
  },
  "rules": {

  }
}
```

- `"root": true` this tells eslint this is the project directory and it should not look any further than this
- `"extends": [ "eslint:recommended" ]` are eslint recommended rules. airbnb's rules can also be used
- `"extends": [ "plugin:import/error" ]` this will throw an error if there are any ES6 import errors
- `parserOptions` - defines the version of js that is uesd. the options declared here are that ES7 or ES2016 is used and javascript standard modules
- `env` - tells eslint to expect certain global variables like Window in the browser
- `rules` - any rules that we want to overwrite
```json
"rules": {
	"no-console": 1
}
```
- `0` === off, `1` === warning, `2` === error
- adds a lint script to the npm package: `"lint": "esw webpack.confing.* src buildScripts",`
- this will use the `eslit-watch` plugin to lint all the files in the src, buildScripts and the webpack.config files
- `/* eslint-disable no-console */` added to the top of the file will disable the console.log warnings in the rest of the file
- `//eslint-disable-line no-console` will disable linting on only one line
- adds a lint watch script the npm package: `"lint:watch": "npm run lint -- --watch",`. This will run the linter every time we hit save
- modify the start script to also include linting: `"start": "npm-run-all --parallel security-check open:src lint:watch",`

___Packages and tools used:___
- packages: `eslint`, `eslint-watch`, `eslint-plugin-import`


## Testing

#### Testing frameworks:
[mocha](https://mochajs.org/), [jasmine](https://jasmine.github.io/), [tape](https://github.com/substack/tape), [qUnit](https://qunitjs.com/), [ava](https://github.com/avajs/ava), [jest](https://facebook.github.io/jest/)

#### Assertion library:
- a way to declare what you expect
- libraries: [chai](http://chaijs.com

#### Helper libraries
- [jsdom](https://github.com/tmpvar/jsdom) - "jsdom is a pure-JavaScript implementation of many web standards, notably the WHATWG DOM and HTML Standards, for use with Node.js
- run dom-related tests without the browser
- [cheerio](https://cheerio.js.org/) - test that a certain html is where you expected

#### Where to run tests?
- tools for running in the browser: [karma](https://karma-runner.github.io/1.0/index.html), [testem](https://github.com/testem/testem)
- tools for headless browser testing:
[phantomjs](http://phantomjs.org/)
- in-memory DOM: [jsdom](https://github.com/tmpvar/jsdom)

#### Tools for unit testing:
- Mocha framework
- Chai assertion library
- JSDOM helper library
- tests run in Node
- test cases are placed alongside each file
- test run on save

#### Setup
- create file that will setup tests: `/buildScripts/testSetup.js`
```javascript
// ES5 and commonJS cuz no transpilation

// Register babel to transpile before the tests run
require( "babel-register" )();
require( "isomorphic-fetch"); // required otherwise it will throw an error

// Disable webpack features that mocha does not understand
require.extensions[ ".css" ] = function() {};
require.extensions[ ".scss" ] = function() {};
```
- use [babel-register](https://www.npmjs.com/package/babel-register) "The require hook will bind itself to node's require and automatically compile files on the fly"
- create a `test` script in npm package.json:
```json
...
"test": "mocha --reporter progress buildScripts/testSetup.js \"src/**/*.test.js\"",
...
```
- Breakdown:
	- `--reporter progress` sets de styling and messages that will be displayed in the terminal when the tests run
	- `buildScripts/testSetup.js` run the setup script
	- `\"src/**/*.test.js\"` - run any tests that can be found in our source directory and any subdirectory. and run any file that has the `.test.js` extension

#### Writing tests
- important to note that when creating async tests, mainly with jsdom, `it` should be passed a `done` parameter and called when the assertion is ready to be tested:
```javascript
import jsdom from "jsdom";
import fs from "fs";

const { JSDOM } = jsdom;
...
describe( "index.html", () => {
	it( "should say hello", ( done ) => {
		const index = fs.readFileSync( "./src/index.html", "utf-8" );
		const dom = new JSDOM( index );
		const h1 = dom.window.document.querySelector( "h1" );
		done();
		expect( h1.innerHTML ).to.equal( "hello world!" );
	} );
} );
```

#### Running tests
- create a test script that runs in watch mode meaning that it will be run every time we hit save: `"test:watch": "npm run test -- --watch"`
- add the watch test to the main start script: `"start": "npm-run-all --parallel security-check open:src lint:watch test:watch",`

#### Continuous integration
- tools: Travis, Jenkins, Appveyor, CircleCI, Semaphore, SnapCI

## HTTP calls
- to use fetch in the client just `import "whatwg-fetch"` in the file
- inside the `fetch` promise there is a variable called `global` that will represent the `window` in the browser or something else on `node`

```javascript
getUsers().then( ( users ) => {
	...

	global.document.querySelector( ".users" ).innerHTML = usersBody;
} );
```

#### Mock API

## Generating mock data
- it uses JSON Schema Faker (`jsf`) to create fake json data
- to use it just `npm install json-schema-faker faker chance --save-dev`
- `jsf` works with a [json configuration object](https://gist.github.com/coryhouse/300ed803148caaf9d4f3f45d1a03874d)
- newer implementations of `jsf` no longer supports `faker` and `chance` libraries
- `jsf` needs to be extended with these libraries
```javascript
jsf.extend( "faker", () => {
	const faker = require( "faker" );
	return faker;
} );

jsf.extend('chance', function(){
  const Chance = require('chance');
  const chance = new Chance();

  return chance;
});
```

- `jsf` works asynchronously and can be invoked like this:
```javascript
jsf.resolve( schema ).then( ( sample ) => {
	const json = JSON.stringify( sample )

	fs.writeFile( "./src/api/db.json", json, function( err ) {
		if( err ) {
			return console.log( chalk.red( err ) );
		} else {
			console.log( chalk.green( "Mock data generated." ) );
		}
	} )
} );
```
- create a npm script: `"generate-mock-data": "babel-node buildScripts/generateMockData",`

## Running a mock JSON server
- it uses `json-server` to serve the data
- create an npm script: `"start-mockapi": "json-server --watch src/api/db.json --port 3001"`

___Packages and tools used:___
- npm packages: `whatwg-fetch json-schema-faker`
- [polyfill.io](https://polyfill.io)
- nock
- [JSON Schema](http://json-schema.org/)
- [JSON Schema Faker](https://github.com/json-schema-faker/json-schema-faker)
- generating random data: [faker.js](https://rawgit.com/Marak/faker.js/master/examples/browser/index.html), [chance.js](http://chancejs.com/), [randexp.js](https://github.com/fent/randexp.js?)
- [JSON Server](https://github.com/typicode/json-server) for serving static json data


## Building for production
### Minification and Sourcemaps
- create file [webpac.config.prod.js](https://github.com/sebikovacs/jsdevenv-test/blob/master/webpack.config.prod.js)

### Running the production build on local machine
- create file `/buildScripts/distServer.js` where we will have an express server that serves the static built files
- change server so it serves static files from the `dist` server
- send the index file from the dist folder for every request
- use compress to gzip the returned file
```javascript
app.use( compression() );
app.use( express.static( "dist" ) );

app.get( "/", function( req, res ) {
	res.sendFile( path.join( __dirname, "../dist/index.html" ) );
} );
```

- toggle the mock api by tweaking the getBaseUrl function to serve the api based on a query parameter in the url

- add a `build` scripts:
```json
"clean-dist": "rimraf ./dist && mkdir dist",
"prebuild": "npm-run-all clean-dist lint test",
"build": "babel-node buildScripts/build.js",
"postbuild": "babel-node buildScripts/distServer.js"
```
	1. remove dist folder and create it again
	2. lint and test the code
	3. run the build.js file
	4. start the production/dist server

### Dynamic html generation
- using html-webpack-plugin
```javascript
plugins: [
	...
	new HtmlWebpackPlugin( {
		template: "src/index.html",
		inject: true,
		minify: {
			removeComments: true,
			collapseWhitespace: true,
			removeRedundantAttributes: true,
			useShortDoctype: true,
			removeEmptyAttributes: true,
			removeStyleLinkTypeAttributes: true,
			keepClosingSlash: true,
			minifyJS: true,
			minifyCSS: true,
			minifyURLs: true
		},
		trackJSToken: "ef5xxxx"
	} ),
	...
]
```

### Bundle splitting
- in case we want two separate files for the vendor scripts and the application scripts we can use the `webpack.optimize.CommonsChunkPlugin`
```javascript
...
entry: {
  vendor: path.resolve(__dirname, 'src/vendor'),
  main: path.resolve(__dirname, 'src/index')
},
output: {
  path: path.resolve(__dirname, 'dist'),
  publicPath: '/',
  filename: '[name].[chunkhash].js'
},
plugins: [
  // create separate bundles
  new webpack.optimize.CommonsChunkPlugin( {
	  name: "vendor"
  } ),
...
```

### Cache Busting
- this is done with the `webpack-md5-hash` plugin. changes needed in webpack.config.prod.js:
```javascript
output: {
  path: path.resolve(__dirname, 'dist'),
  publicPath: '/',
  filename: '[name].[chunkhash].js' //chunkhash is a variable that will be substituted with the cachebusting hash
},
plugins: [
	...
	new WebpackMd5Hash(),
	...
]
```


### Extract and minify CSS
- this is done via the `ExtractTextPlugin`
```javascript
plugins: [
	...
	new ExtractTextPlugin( "[name].[contenthash].css" ),// contenthash will be substituted with the cachebusting hash
	...
]
```

### Error Logging
- we do this via Trackjs

### Error Logging
- adding scripts conditionally to the html file is done via `ejs`
```html
<% if ( htmlWebpackPlugin.options.trackJSToken ) { %>
<!-- BEGIN TRACKJS -->
<script type="text/javascript">window._trackJs = { token: '<%= htmlWebpackPlugin.options.trackJSToken %>' };</script>
<script type="text/javascript" src="https://cdn.trackjs.com/releases/current/tracker.js"></script>
<!-- END TRACKJS -->
<% } %>
```

___Packages and tools used:___
- npm packages: `compress rimraf html-webpack-plugin extract-text-webpack-plugin webpack-md5-hash`
- tools: [trackjs](https://trackjs.com/)
