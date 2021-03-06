# Setting up Browserify / Babelify

With this setup you can import modules into your `main.js`. Only code used in `main.js` will be called by the app.


## Steps

### 1. Install the required plugins

```
$ npm install -save babelify vinyl-buffer vinyl-source-stream
```

### 2. Edit your `scripts` task

```diff
 import browserSync from 'browser-sync';
 import del from 'del';
 import {stream as wiredep} from 'wiredep';
+import browserify from 'browserify';
+import babelify from 'babelify';
+import buffer from 'vinyl-buffer';
+import source from 'vinyl-source-stream';
```

```diff
gulp.task('scripts', () => {
-  return gulp.src('app/scripts/**/*.js')
+
+  const b = browserify({
+    entries: 'app/scripts/main.js',
+    transform: babelify,
+    debug: true
+  });
+
+  return b.bundle()
+    .pipe(source('bundle.js'))
     .pipe($.plumber())
-    .pipe($.sourcemaps.init())
-    .pipe($.babel())
+    .pipe(buffer())
+    .pipe($.sourcemaps.init({loadMaps: true}))
     .pipe($.sourcemaps.write('.'))
     .pipe(gulp.dest('.tmp/scripts'))
     .pipe(reload({stream: true}));
});
```

### 3. Edit your `index.html` 

We are going to require the compiled bundle.

```diff
<!-- endbuild -->

     <!-- build:js scripts/main.js -->
-    <script src="scripts/main.js"></script>
+    <script src="scripts/bundle.js"></script>
     <!-- endbuild -->
   </body>
 </html>
```

## Usage

- Put your modules in `app/scripts`, and require them in your `main.js`.

For example, you could have `foo.js`:

```js
module.exports.speak = () => console.log('hello world');
```

And then in your `main.js`:

```js
const foo = require('./foo');
foo.speak();
```

