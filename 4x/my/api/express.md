express တစ်ခု application တည်ဆောက်ပုံ

```js
var express = require('express');
var app = express();

app.get('/', function(req, res){
  res.send('hello world');
});

app.listen(3000);
```