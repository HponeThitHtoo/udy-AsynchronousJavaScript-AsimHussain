# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");

function readFile(filename, encoding) {
  fs.readFile(filename, encoding, (err, data) => {
    //TODO
  });
}
readFile("./files/demofile.txt", "utf-8")
    .then(...)
});
```

# Question 2

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  zlib.gzip(data, (error, result) => {
    //TODO
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

readFile("./files/demofile.txt", "utf-8")
    .then(...) // --> Load it then zip it and then print it to screen
});
```

# Question 3

Convert the previous code so that it now chains the promise as well.

```js
const fs = require("fs");
const zlib = require("zlib");
const util = require("util");

// const readFile = util.promisify(fs.readFile);
// const gzip = util.promisify(zlib.gzip);

function gzip(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (err, result) => {
      if (err) return reject(err);
      resolve(result);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) return reject(err);
      resolve(data);
    });
  });
}

// Starting to look like callback hell?
readFile("./files/demofile.txt", "utf-8").then(
  data => {
    return gzip(data);
  },
  err => console.error("Failed To Read", err)
).then(
  res => {
    console.log(res)
  }, 
  err => {
    console.error("Failed To Zip", err)
  }
);
```

# Question 4

Convert the previous code so that it now handles errors using the catch handler

```js
const fs = require("fs");
const zlib = require("zlib");
const util = require("util");

// const readFile = util.promisify(fs.readFile);
// const gzip = util.promisify(zlib.gzip);

function gzip(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (err, result) => {
      if (err) return reject(err);
      resolve(result);
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) return reject(err);
      resolve(data);
    });
  });
}

// Starting to look like callback hell?
readFile("./files/demofile.txt", "utf-8")
  .then(data => {
      return gzip(data);
    }).then(res => {
      console.log(res)
    })
    .catch(err => console.error("Failed: ", err));
```

# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise(resolve => setTimeout(resolve, sleep));
}

readFileFake(5000); // This resolves a promise after 5 seconds, pretend it's a large file being read from disk
```

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 3 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 200 }));
}

function publish() {
  console.log("Publishing");
  return new Promise(resolve => setTimeout(resolve, 2000, { status: 403 }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, "timeout"));
}

Promise.race( [publish(), timeout(1000)])
  .then(res => {
    if (res.status === 403)
      return authenticate();
  })
  .then(_ => console.log("Published"))
  .catch(err => {
    if (err === "timeout")
      console.error("Request timed out");
    else
      console.error(err)
  });
```
