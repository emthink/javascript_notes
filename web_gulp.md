# Grunt与Gulp
对于我们前端开发者，目前使用最多的自动化管理工具应该是Grunt--在Gruntfile.js文件中使用JavaScript定义各种任务。

## Gulp
基于Node.js流，配置简单，易于学习，使用方便。在guplfile.js里按定义好的规则执行构建任务。学习Gulp之前先来了解一下Vinyl和Orchestrator

### Vinyl
Vinyl是一种虚拟文件格式，是一种简单的描述文件的元数据对象。主要有path和contents两个属性。

#### Vinyl adpter
Vinyl对文件进行了描述，而Vinyl adpter通过提供src和dest方法支持我们对文件进行访问。

```

	var map = require('map-stream');
	var vfs = require('vinyl-fs');
	
	var log = function(file, cb) {
	  console.log(file.path);
	  console.log(file.contents);
	  cb(null, file);
	};
	vfs.src(['./js/**/*.js', '!./js/except/*.js'])
	  .pipe(map(log))
	  .pipe(vfs.dest('./output'));
```

### Orchestrator

一个指定任务和依赖的关系与处理，然后并发执行任务的模块。

```

	var Orchestrator = require('orchestrator');
	var orchestrator = new Orchestrator();
	orchestrator.add('thing1', function(){
	  // do stuff
	});
	orchestrator.add('thing2', function(){
	  // do stuff
	});
	orchestrator.add('thing3', ['thing2'], function(){
	  // do stuff
	});
	orchestrator.start('thing1', 'thing3', function (err) {
	  // all done
	});
```

### Gulp五个API

- gulp.src()
	
	接受一个匹配模式或一个匹配模式数组，返回对应的Viny files流，可以当作其他插件的输入。
	
	```
	
		var gulp = require('gulp')
		, jshint = require('gulp-jshint')
		, concat = require('gulp-concat')
		, uglify = require('gulp-uglify')
		, rename = require('gulp-rename');
		gulp.src(['./app/src/js/**/*.js', '!./app/src/js/**/*.min.js'])
		.pipe(rename({suffix: '.min'}))
		.pipe(uglify())
		.pipe(gulp.dest('./app/build/js'));
	```
	
- gulp.dest()

	接收一个路径参数（可以由函数返回一个路径），，输出数据，写入文件。

- gulp.task()

	定义gulp任务，并制定任务依赖及执行规则。Gulp的任务操作继承自Orchestrator。
	
	```
	
		//gulp source code
		var util = require('util');
		var Orchestrator = require('orchestrator');
		function Gulp() {
		  Orchestrator.call(this);
		}
		util.inherits(Gulp, Orchestrator);
		Gulp.prototype.task = Gulp.prototype.add;
	```
	
	简单声明依赖后，任务函数依顺序执行：
	```
	
		gulp.task('compileLESS', ['clean'], function(){
		    //compile LESS file...
		});
		gulp.task('clean', function(){
		    gulp.src('sample.css', { read:false })
		        .pipe(clean());
		});
	```
	
	保证任务顺序执行（返回流）：
	```
	
		gulp.task('compileLESS', ['clean'], function(){
		    //compile LESS file...
		});
		gulp.task('clean', function(){
		    return gulp.src('sample.css', { read:false })
		        .pipe(clean());
		});
	```
	
- gulp.run()

	接收一个任务名，执行该任务。

- gulp.watch()

	接收一个匹配模式或匹配模式数组，可选的传入一个回调函数，返回一个EventEmitter发送change事件；监听匹配文件的变化，一旦文件发生变化就促发回调。
	
	```
	
		var watcher = gulp.watch('js/**/*.js', ['uglify','reload']);
		watcher.on('change', function(event) {
		  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
		});
	```
	
	```
	
		gulp.watch('./app/src/js/**/*.js', function() {
			gulp.run('lint', 'scripts');
		});
	```

### Grunt VS Gulp

无论grunt还是gulp亦或是webpack，npm scripts，它们都只是一个工具，让我们更专注在代码上，而不应该是迷失在众多工具中。尽管如此，在个人使用中，grunt与gulp还是有差别，主要从两方面简要说明：

- 配置和使用

	- gulp基于CommonJs规范，gulpfile.js配置更简单、代码更适于维护。
	- gulp的五个API清晰简单，使用更简单。
	- grunt插件更丰富、功能更全面。

- 底层文件操作

	- grunt以临时文件的形式，从源文件，生成多个临时文件，根据不同任务进行多次读写，最后生成目标文件，I/O操作较频繁。
	- gulp以流的形式，从源文件一次读操作，通过管道方式使用内存中的文件流在不同任务间传递，最后写入目标文件I/O操作较少，