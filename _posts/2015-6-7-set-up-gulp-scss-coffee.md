---
layout: post
title: Setup môi trường làm web với gulp
comments: true
category: programming
tags: [html, css]
---

## Install Gulp

* Install toàn cục(global): ```npm install --global gulp```
* Install cục bộ trong dự án: ```npm install --save-dev gulp```

## Install Plugin cho HTML

* jade: ```npm install --save-dev gulp-jade```

## Install Plugin cho CSS

* gulp-sass: ```npm install gulp-sass --save-dev```
* gulp-pleeease: ```npm install gulp-pleeease --save-dev```
* gulp-csscomb: ```npm install gulp-csscomb --save-dev```
* gulp-cssmin: ```npm install gulp-cssmin --save-dev```

## Install Plugin cho Javascript

* gulp-coffee: ```npm install gulp-coffee --save-dev```
* gulp-concat: ```npm install gulp-concat --save-dev```
* gulp-uglify: ```npm install gulp-uglify --save-dev```

## Install Plugin tiện ích khác

* gulp-data: ```npm install gulp-data --save-dev```
* gulp-sitemap-files: ```npm install gulp-sitemap-files --save-dev``
* run-sequence: ```npm install run-sequence --save-dev```
* gulp-plumber: ```npm install gulp-plumber --save-dev```
* gulp-header: ```npm install gulp-header --save-dev```
* BrowserSync + Gulp.js: ```npm install browser-sync gulp --save-dev```
* del: ```npm install del --save-dev``` *tham khảo https://github.com/google/web-starter-kit/blob/master/gulpfile.js*

## gulpfile.js

```js
'use strict';
var gulp = require('gulp');

var browserSync = require('browser-sync');
var reload = browserSync.reload;

var data = require('gulp-data');
var plumber = require('gulp-plumber');

var sass = require('gulp-sass');
var pleeease = require('gulp-pleeease');
var csscomb = require('gulp-csscomb');
var cssmin = require('gulp-cssmin');

var jade = require('gulp-jade');

var sitemap = require('gulp-sitemap');

var del = require('del');
var runSequence = require('run-sequence');
var header  = require('gulp-header');

var coffee  = require('gulp-coffee');
var concat  = require('gulp-concat');
var uglify  = require('gulp-uglify');

var AUTOPREFIXER_BROWSERS = [
  'ie >= 10',
  'ff >= 30',
  'chrome >= 34',
  'safari >= 7',
  'opera >= 23',
];
var pkg = require('./package.json');
var BANNER = [
  '@charset "utf-8";',
  '/**',
  ' * <%= pkg.name %> - <%= pkg.description %>',
  ' * @link <%= pkg.url %>',
  ' * @version v<%= pkg.version %>',
  ' * @Author <%= pkg.author %>',
  ' * @Author URI <%= pkg.author %>',
  ' */',
  ''
].join('\n');

gulp.task('default', function () {
  browserSync({
    notify: false,
    port: 3000,
    server: {
      baseDir: ['./dist/']
    }
  });
  gulp.watch(['./src/scss/*.scss','./src/scss/**/_*.scss'],['sass']);
  gulp.watch(['./src/jade/*.jade','./src/jade/**/*.jade','src/jade/**/_*.jade'],['jade']);
  gulp.watch(['./src/coffee/*.coffee','./src/coffee/**/_*.coffee'],['coffee']);
  gulp.watch(['./src/js/*.js'],['compile-js']);
});


gulp.task('sass', function () {
  gulp.src(['src/scss/*.scss','src/scss/**/_*.scss'])
    .pipe(plumber())
    .pipe(sass())
    .pipe(pleeease({
      minifier: false,
      autoprefixer: 'chrome >= 39'
    }))
    .on('error', console.error.bind(console))
    .pipe(header('@charset "utf-8";\n'))
    .pipe(gulp.dest('./dist/'))
    .on('end', reload);
});

gulp.task('sass:deproy', function () {
  gulp.src(['src/scss/*.scss','src/scss/**/_*.scss'])
    .pipe(sass())
    .pipe(pleeease({
      autoprefixer: AUTOPREFIXER_BROWSERS,
      minifier: false
    }))
    .pipe(csscomb())
    .pipe(cssmin())
    .pipe(header(BANNER, { pkg : pkg } ))
    .pipe(gulp.dest('./dist/'));
});

gulp.task('jade', function () {
  gulp.src(['./src/jade/*.jade','src/jade/**/*.jade','!src/jade/**/_*.jade'])
    .pipe(plumber())
    .pipe(data(function(file) {
      return require('./data.json');
    }))
    .pipe(jade({
      pretty: true
    }))
    .pipe(gulp.dest('./dist/'))
    .on('end', reload);
});

gulp.task('coffee', function () {
  gulp.src(['./src/coffee/*.coffee','./src/coffee/**/_*.coffee'])
    .pipe(plumber())
    .pipe(coffee({ bare: true }))
    .on('error', console.error.bind(console))
    .pipe(gulp.dest('./src/js/'))
});

gulp.task('compile-js',function () {
  var compileFileName = 'application.js'
  gulp.src(['./src/js/*.js','!src/js/' + compileFileName])
    .pipe(uglify())
    .pipe(concat(compileFileName))
    .pipe(gulp.dest('./dist/assets/js/'))
    .on('end', reload);
});

gulp.task('sitemap', function () {
  gulp.src('./dist/**/*.html')
    .pipe(sitemap({
      siteUrl: 'http://www.solana.asia/'
    }))
    .pipe(gulp.dest('./dist/'));
});

gulp.task('clean', function(cb) {
  del(['./dist/**/*.html','./dist/**/*.css','./dist/**/*.xml'], cb);
});

// ['clean'] の後サイトマップはうまく出力されない。タイミングの問題だろうけど特に使うこと無かったからこのまま
gulp.task('deproy',['clean'],function(cb) {
  runSequence(
    ['sass:deproy','jade'],
    'sitemap',
    cb
  );
});
```

