### Explanation
> Start a new project

### Install node js
> https://nodejs.org/

### Update node js to latest version
> sudo npm install npm -g

### Initiate npm
> npm init

### Install gulp
> npm install -g gulp

### Install gulp and add dependency
> npm install --save-dev gulp
—save-dev 將dependency寫入package.json

### Install other packages
> npm install --save-dev browserify vinyl-source-stream gulp-minify-css gulp-notify fs gulp-uglify reactify gulp-jasmine gulp-less path

### Add script to package.json for starting gulp

```js

"scripts": {
    "start": "gulp"
  }

```

### Gulp file setting

```js

var gulp = require('gulp');
var browserify = require('browserify');
var source = require('vinyl-source-stream');
var minifyCSS = require('gulp-minify-css');
var notify = require('gulp-notify');
var fs = require('fs');
var uglify = require('gulp-uglify');
var reactify = require('reactify');
var less = require('gulp-less');
var path = require('path');


var paths = {
    main: './app/js/boot.js',
    css: './app/assets/css/*.css',
    destDir: 'build',
    destCSS: 'build/assets/css'
};


function buildJS(boot, result){

    return browserify({
        entries:[ boot ],
        debug: true
    })
        .bundle()
        .on('error', function( err ){
            console.log( '[ERROR]', err );
            this.end();
            gulp.src('').pipe( notify('✖ Bunlde Failed ✖') )
        })
        .pipe( source(result) )
        .pipe( gulp.dest('./build') );
}

gulp.task('app-js', function(){
    return buildJS('./app/js/app.jsx' , 'app.js');
});



gulp.task('contract-js', function() {

    return buildJS('./app/js/contract_contract_boot.js' , 'contract_contract.js');

});

gulp.task('asset-js', function() {

    return buildJS('./app/js/asset_boot.js' , 'asset.js');

});

gulp.task('management-js', function() {

    return buildJS('./app/js/management_boot.jsx' , 'management.js');

});

gulp.task('publish-js', function() {

    return buildJS('./app/js/publish_boot.jsx' , 'publish.js');

});

gulp.task('buildFiles',['app-js']);

gulp.task('compress', function() {
    return gulp.src('./build/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('./build'));
});

gulp.task('copy-html', function(){
    gulp.src([ 'app/*.html' ], { base: 'app' } )
        .pipe( gulp.dest(paths.destDir));
});

gulp.task('copy-assets-js', function(){
    gulp.src('app/assets/**/*.*')
        .pipe( gulp.dest(paths.destDir+'/assets'));
});

gulp.task('watch', function() {
    gulp.watch( 'app/**/*', ['buildFiles', 'minify-css', 'copy-html', 'copy-assets-js'] );
});


gulp.task('bundle-js', function() {


    return browserify({
        entries:[ paths.main ]
    })

        .transform( 'reactify' )

        .bundle({debug: true})

        .on('error', function( err ){
            console.log( '[錯誤]', err );
            this.end();
            gulp.src('').pipe( notify('✖ Bunlde Failed ✖') )
        })

        .pipe( source('bundle.js') )

        .pipe( gulp.dest('./build') )

});

gulp.task('minify-css', function() {
    gulp.src( paths.css )
        .pipe(minifyCSS(
            {
                noAdvanced: false,
                keepBreaks:true,
                cache: true
            }))
        .pipe(gulp.dest( paths.destCSS ))
});


gulp.task('default', ['task1']);

gulp.task('deployment', ['compress'] );

gulp.task('task1', ['buildFiles', 'minify-css', 'copy-html', 'copy-assets-js', 'watch'] );



```

### How to use

> npm install
> npm start


