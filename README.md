Description
-----------

SOCKS protocol version 5 server and client implementations for node.js

> THIS IS FORK OF <https://github.com/mscdex/socksv5>

Requirements
------------

* [node.js](http://nodejs.org/) -- LTS v6.11.5

Main difference from original release
-------------------------------------

### v1.0.7
+ [BUGFIX] If closed port on socks, this cause an error with a code ESOCKSCONNREFUSED
+ [BUGFIX] If closed port on remote server, this cause an error with a code
  ECONNREFUSED

### v1.0.6
+ Added the option "timeout" into the client config (it works when 
  socks5 connecting to the remote host). Then a "timeout" emitted, will 
  to be also emitted "error" event with the code = "ETIMEOUT"

+ Added the option "proxyTimeout" into the client config (it works when 
  the client host connecting to the socks5 proxy). Then a "timeout" emitted, will 
  to be also emitted "error" event with the code = "ESOCKSTIMEOUT"

Example
-------

```javascript
var socketClient = new socks.Client({
  timeout: 5000,
  proxyTimeout: 5000,
  proxyHost: '127.0.0.1',
  proxyPort: 9150,
  auths: [ socks.auth.None() ]
})

socketClient.connect({
  host: 'smtp.mail.ru',
  port: 587
})

socketClient.on('connect', function(socket){
  console.log('>> Connection successful');

  socket.pipe(process.stdout);

  socket.on('data', data => {
    socket.end();
  })

  socket.on('error', err => {
    console.log('Socket error', err)
  })
})


socketClient.on('error', (err) => {
  if (err.code == 'ESOCKSCONNREFUSED') {
    console.log('SOCKS port closed')
  } else if (err.code == 'ECONNREFUSED') {
    console.log('Remote host port closed')
  } else if (err.code == 'ESOCKSTIMEOUT') {
    console.log('Connection to socks timeout')
  } else if (err.code == 'ETIMEOUT') {
    console.log('Connection to host timeout')
  } else if (err.code == 'ENETUNREACH') {
    console.log('Remote host address error')
  } else {
    console.log(err)
  }
})
```
