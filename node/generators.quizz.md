# Question 1

Create a custom async generator that loops over the files that are passed in.

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

// function* fileLoader(files) {...}

function* fileLoader(files) {
  for (let i = 0; i < files.length; i++) {
    yield readFile(files[i], "utf8");
  }
}

/* See Answer file for alternative solution */

(async () => {
  for await (let contents of fileLoader([
    "./files/demofile.txt",
    "./files/demofile.other.txt"
  ])) {
    console.log(contents);
  }
})();
```
