# ftp-deploy-log

A Node.js package to help with deploying code. Ftp a folder from your local disk to a remote ftp destination. Does not delete from destination directory.

## Installation

```js
npm install ftp-deploy-log
```

(Need sftp? Check out [sftp-upload](https://github.com/pirumpi/sftp-upload))


## Usage

I create a file - e.g. deploy.js - in the root of my source code and add a script to its package.json so that I can `npm run deploy`.

```json
  "scripts": {
    "deploy": "node deploy"
  },
```

The most basic usage (stops uploading when an error occurs):

```js
var FtpDeploy = require('ftp-deploy-log');
var ftpDeploy = new FtpDeploy();

var config = {
	username: "username",
	password: "password", // optional, prompted if none given
	host: "ftp.someserver.com",
	port: 21,
	localRoot: __dirname + "/local-folder",
	remoteRoot: "/public_html/remote-folder/",
	include: ['build/version.txt'],
	exclude: ['.git', '.idea', 'tmp/*', 'build/*'],
  useLog: true,  // set to false if you do not want to track file modified dates to ignore previously 			   // pushed versions of files
  staging: true //set to true if you want to push to the alternate env
}
	
ftpDeploy.deploy(config, function(err) {
	if (err) console.log(err)
	else console.log('finished');
});
```

To be notified of what ftpDeploy is doing:

```js
ftpDeploy.on('uploading', function(data) {
    data.totalFileCount;       // total file count being transferred
    data.transferredFileCount; // number of files transferred
    data.percentComplete;      // percent as a number 1 - 100
    data.filename;             // partial path with filename being uploaded
});
ftpDeploy.on('uploaded', function(data) {
	console.log(data);         // same data as uploading event
});
```

To continue uploading files even if a file upload fails: 

```js
config.continueOnError = true;

ftpDeploy.deploy(config, function(err) {
	if (err) console.log(err) // error authenticating or creating/traversing directory
	else console.log('finished');
});

ftpDeploy.on('upload-error', function (data) {
	console.log(data.err); // data will also include filename, relativePath, and other goodies
});
```
## Testing 

I use proftpd to create a simple ftp server at test/remote and then run the script at `node ./test/test`

## License 

MIT
