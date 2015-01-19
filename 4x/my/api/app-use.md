Mount the [middleware](#middleware.api) `function`(s) at the `path`. If `path` is not specified, it defaults to "/".

<div class="doc-box doc-notice">
A route will match any path, which follows its path immediately with a "<code>/</code>". For example: <code>app.use('/apple', ...)</code> will match <b>/apple</b>, <b>/apple/images</b>, <b>/apple/images/news</b>, and so on.  
</div>

Mounting a middleware at a `path` will cause the middleware function to be executed whenever the base of the requested path matches the `path`.

Since `path` defaults to "/", middleware mounted without a path will be executed for every request to the app.

```js
// this middleware will be executed for every request to the app
app.use(function (req, res, next) {
  console.log('Time: %d', Date.now());
  next();
})
```

Middleware functions are executed sequentially, therefore the order of middleware inclusion is important.

```js
// this middleware will not allow the request to go beyond it
app.use(function(req, res, next) {
  res.send('Hello World');
})

// requests will never reach this route
app.get('/', function (req, res) {
  res.send('Welcome');
})
```

`path` can be a string representing a path, a path pattern, a regular expression to match paths, or an array of combinations of the aforementioned path objects.

<div class="doc-box doc-notice">The middleware examples below are intentionally left overly simple to keep the examples lean and clutter-free.</div>

<table class="doctable" border="1">
  <thead>
      <tr>
        <th> Type </th>
        <th> Example </th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>** Path **</td>
        <td>
          <pre><code class="lang-js">// will match paths starting with /abcd
app.use('/abcd', function (req, res, next) {
  next();
})</code></pre>
      </tr>

      <tr>
        <td>** Path Pattern **</td>
        <td>
          <pre><code class="lang-js">// will match paths starting with /abcd and /abd
app.use('/abc?d', function (req, res, next) {
  next();
})

// will match paths starting with /abcd, /abbcd, /abbbbbcd and so on
app.use('/ab+cd', function (req, res, next) {
  next();
})

// will match paths starting with /abcd, /abxcd, /abFOOcd, /abbArcd and so on
app.use('/ab\*cd', function (req, res, next) {
  next();
})

// will match paths starting with /ad and /abcd
app.use('/a(bc)?d', function (req, res, next) {
  next();
})</code></pre>
        </td>
      </tr>

      <tr>
        <td>** Regular Expression **</td>
        <td>
          <pre><code class="lang-js">// will match paths starting with /abc and /xyz
app.use(/\/abc|\/xyz/, function (req, res, next) {
  next();
})</code></pre>
        </td>
      </tr>

      <tr>
        <td>** Array **</td>
        <td>
          <pre><code class="lang-js">// will match paths starting with /abcd, /xyza, /lmn, and /pqr
app.use(['/abcd', '/xyza', /\/lmn|\/pqr/], function (req, res, next) {
  next();
})</code></pre>
        </td>
      </tr>

    </tbody>
</table>

`function` can be a middleware function, a series of middleware functions, an array of middleware functions, or a combination of all of them. Since routers and apps implement the middleware interface, they can be used like any other middleware function.

<table class="doctable" border="1">
  <thead>
    <tr>
      <th>Usage</th>
      <th>Example</th>
    </tr>
  </thead>
  <tbody>

    <tr>
      <td>** Single Middleware **</td>
      <td>
A middleware function can be defined and mounted locally.
<pre><code class="lang-js">app.use(function (req, res, next) {
  next();
})
</code></pre>
A router is a valid middleware.

<pre><code class="lang-js">var router = express.Router();
router.get('/', function (req, res, next) {
  next();
})
app.use(router);
</code></pre>

An Express app is a valid middleware.
<pre><code class="lang-js">var subApp = express();
subApp.get('/', function (req, res, next) {
  next();
})
app.use(subApp);
</code></pre>
    </tr>

    <tr>
      <td>**Series of Middleware**</td>
      <td>
        More than one middleware can be specified at a mount path.
<pre><code class="lang-js">var r1 = express.Router();
r1.get('/', function (req, res, next) {
  next();
})

var r2 = express.Router();
r2.get('/', function (req, res, next) {
  next();
})

app.use(r1, r2);
</code></pre>
    </tr>

    <tr>
      <td>** Array **</td>
      <td>
        Clubbing middleware in arrays is a good way to logically group them. The mount path has to be specified, if an array of middleware is passed as the first or the only set of middleware.
<pre><code class="lang-js">var r1 = express.Router();
r1.get('/', function (req, res, next) {
  next();
})

var r2 = express.Router();
r2.get('/', function (req, res, next) {
  next();
})

app.use('/', [r1, r2]);
</code></pre>
      </td>
    </tr>

    <tr>
      <td>** Combination **</td>
      <td>
        All the above ways of mounting middleware can be combined.
<pre><code class="lang-js">function mw1(req, res, next) { next(); }
function mw2(req, res, next) { next(); }

var r1 = express.Router();
r1.get('/', function (req, res, next) { next(); });

var r2 = express.Router();
r2.get('/', function (req, res, next) { next(); });

var subApp = express();
subApp.get('/', function (req, res, next) { next(); });

app.use(mw1, [mw2, r1, r2], subApp);
</code></pre>
      </td>
    </tr>

  </tbody>
</table>

Following are some examples of using the [express.static](#express.static) middleware in an Express app.

Serve static content for the app from the "public" directory in the application directory.

```js
// GET /style.css etc
app.use(express.static(__dirname + '/public'));
```

Mount the middleware at "/static" to serve static content only when their request path is prefixed with "/static".

```js
// GET /static/style.css etc.
app.use('/static', express.static(__dirname + '/public'));
```

Disable logging for static content requests by loading the logger middleware after the static middleware.

```js
app.use(express.static(__dirname + '/public'));
app.use(logger());
```

Serve static files from multiple directories, but give precedence to "./public" over the others.

```js
app.use(express.static(__dirname + '/public'));
app.use(express.static(__dirname + '/files'));
app.use(express.static(__dirname + '/uploads'));
```
