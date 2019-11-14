# self-signed-httpand-https-server-express-nodejs

----

## Create a self signed certificate using OpenSSL , I am testing this on ubuntu Machine .

### Generate Private key->
    openssl genrsa -out localhost.key 2048

### create a CSR for sigining request to get the certificate .

    openssl req -new -x509 -key localhost.key -out localhost.cert -days 3650 -subj /CN=localhost

### Genarate an express default setup 

    express cert-app

### Enter into the directory

    cd cert-app

### Move recently created certificate and key .key and .cert file into the same directory on root level where express app residing

### Go to bin/www and paste the below code there and replace old one completely 

```

/**
 * Module dependencies.
 */

var app = require('../app');
var debug = require('debug')('cert-app:server');
var http = require('http');
var https = require('https');
var fs = require('fs');

/**
 * Get port from environment and store in Express.
 */

var port = normalizePort(process.env.PORT || '3000');
//app.set('port', port);

/**
 * Create HTTP server.
 */
var options = {
  key: fs.readFileSync( './localhost.key'),
  cert: fs.readFileSync( './localhost.cert'),
  requestCert: false,
  rejectUnauthorized: false,
};

var server = http.createServer(app).listen(3001);
var server2 = https.createServer(options, app).listen(3000);
/**
 * Listen on provided port, on all network interfaces.
 */

server.listen(port);
server.on('error', onError);
server.on('listening', onListening);

/**
 * Normalize a port into a number, string, or false.
 */

function normalizePort(val) {
  var port = parseInt(val, 10);

  if (isNaN(port)) {
    // named pipe
    return val;
  }

  if (port >= 0) {
    // port number
    return port;
  }

  return false;
}

/**
 * Event listener for HTTP server "error" event.
 */

function onError(error) {
  if (error.syscall !== 'listen') {
    throw error;
  }

  var bind = typeof port === 'string'
    ? 'Pipe ' + port
    : 'Port ' + port;

  // handle specific listen errors with friendly messages
  switch (error.code) {
    case 'EACCES':
      console.error(bind + ' requires elevated privileges');
      process.exit(1);
      break;
    case 'EADDRINUSE':
      console.error(bind + ' is already in use');
      process.exit(1);
      break;
    default:
      throw error;
  }
}

/**
 * Event listener for HTTP server "listening" event.
 */

function onListening() {
  var addr = server.address();
  var bind = typeof addr === 'string'
    ? 'pipe ' + addr
    : 'port ' + addr.port;
  debug('Listening on ' + bind);
}

```