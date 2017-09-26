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

```
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
