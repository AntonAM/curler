# curler
A native c++ node.js module for asynchronous http requests via libcurl.

## Install
<pre>
  $ npm install curler
</pre>

## request(options, callback[err, res, bodyData])

### Options
 - `url`: request url. (required)
 - `method`: HTTP method type. Defaults to `GET`. (can be anything)
 - `headers`: Optional JSON key/value array of the request headers.
 - `customOpts`: custom options (CURLOPT_*) for libcurl. See code exmple below.
 - `data`: Optional request body data.
 - `timeout`: Total request timeout (connection/response) in milliseconds.
 - `connectionTimeout`: Connection timeout in milliseconds.
 - `maxRedirects`: if not 0 libcurl will follow up to 'maxRedirects' redirections.

## Examples

### GET request
``` js
var curler = require("curler").Curler;
var curlClient = new curler();
var CURLOPT_HEADER = 42;  //* From 'curl.h'


console.log(curlClient.version());

var custom = {};
custom[CURLOPT_HEADER] = 1; //* Show result headers.

var options = {
  method: "GET",
  url: 'http://www.google.com',
  maxRedirects: 5,
  customOpts: custom
};

var startDate = Date.now();
curlClient.request(options, function(err, res, bodyData) {
  var duration = (Date.now() - startDate);
  if (err) {
    console.log(err);
  }
  else {
    console.log('statusCode: %s', res.statusCode);
    console.log('bodyData: %s', bodyData);
  }

  console.log("curler (libcurl) performed http request in %s ms. dnsTime: %s, connectTime: %s, preTransferTime: %s, startTransferTime: %s, redirectTime: %s, totalTime: %s", duration, res.dnsTime, res.connectTime, res.preTransferTime, res.startTransferTime, res.redirectTime, res.totalTime);
});
```

### POST request (body data)
``` js
var curler = require("curler").Curler;
var curlClient = new curler();

var data = JSON.stringify({ hello: 'world' });

var options = {
  method: "POST",
  url: 'http://www.example.com/',
  headers: {
    'Content-Type': 'application/json',
    'Connection': 'Keep-Alive'
  },
  data: data,
  timeout: 5000,
  connectionTimeout: 5000
};

var startDate = Date.now();
curlClient.request(options, function(err, res, bodyData) {
  var duration = (Date.now() - startDate);
  if (err) {
    console.log(err);
  }
  else {
    console.log('statusCode: %s', res.statusCode);
    console.log('bodyData: %s', bodyData);
  }

  console.log("curler (libcurl) performed http request in %s ms. dnsTime: %s, connectTime: %s, preTransferTime: %s, startTransferTime: %s, totalTime: %s", duration, res.dnsTime, res.connectTime, res.preTransferTime, res.startTransferTime, res.totalTime);
});
```

## TODO
- Proxy support (http/https)
- Allow Expect: 100-Continue to be configurable, rather than always off
- Load a queue of curl handles when the module loads (ghetto connection pooling). Need a deconstructor in curler.cc that works first!