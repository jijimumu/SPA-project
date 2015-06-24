### 說明
> Start a new project

### 安裝 node js
> https://nodejs.org/

### 更新 node js 至最新版本
> sudo npm install npm -g

### 初始化 npm
> npm init

### 安裝 gulp 並新增 dependency
> npm install --save-dev gulp
—save-dev 將dependency寫入package.json

### 安裝其他套件
> npm install --save-dev browserify vinyl-source-stream gulp-minify-css gulp-notify fs gulp-uglify reactify gulp-jasmine gulp-less path

### 新增 script 至 package.json 啟動 starting gulp

```js

"scripts": {
    "start": "gulp"
  }

```

### Gulp file setting

```js

var gulp = require('gulp'); 
var browserify = require('browserify'); 
var source = require('vinyl-source-stream'); //重新命名用
var minifyCSS = require('gulp-minify-css'); //壓縮css
var notify = require('gulp-notify'); //log用
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

> git clone https://github.com/jijimumu/SPA-project.git

> 進入 SPA-project 資料夾

> npm install

> npm start


