# Gulp Workflow

A workflow for building web projects with [Gulp](https://gulpjs.com/). Uses Gulp 4.x.

**Features**

- Concatenate, minify, and lint JavaScript.
- Compile, minify, autoprefix, and lint Sass.
- Optimize `SVGs`.
- Compress and optimize `JPEG` and `PNG` image files.
- Convert and optimize `JPEG` and `PNG` images to `WebP` files.
- Copy static files and folders into your `dist` directory.
- Automatically add headers and project details to JS and CSS files.
- Create polyfilled and non-polyfilled versions of JS files.
- Watch for file changes, and automatically recompile build and reload webpages.

**Gulp workflow makes it easy to turn features on and off**, so you can reuse it for all of your projects without having to delete or modify tasks.

## Getting Started

### Dependencies

_**Note:** if you've previously installed Gulp globally, run `npm rm --global gulp` to remove it. [Details here.](https://medium.com/gulpjs/gulp-sips-command-line-interface-e53411d4467)_

Make sure these are installed first.

- [Node.js](http://nodejs.org)
- [Gulp Command Line Utility](http://gulpjs.com) `npm install --global gulp-cli`

### Quick Start

1. In terminal/command/bash, `cd` into your project directory.
2. Run `npm install` to install required files and dependencies.
3. When it's done installing, run one of the task runners to get going:
   - `gulp` manually compiles files.
   - `gulp watch` automatically compiles files and applies changes using [BrowserSync](https://browsersync.io/) when you make changes to your source files.

**Try it out.** After installing, run `gulp` to compile some test files into the `dist` directory. Or, run `gulp watch` and make some changes to see them recompile automatically.

## Documentation

Add your source files to the appropriate `src` subdirectories. Gulp will process and and compile them into `dist`.

- JavaScript files in the `src/js` directory will be compiled to `dist/js`. Files in subdirectories under the `js` folder will be concatenated. For example, files in `js/detects` will compile into `detects.js`.
- Files in the `src/sass` directory will be compiled to `dist/css`.
- SVG files placed in the `src/svg` directory will be optimized with SVGO and compiled into `dist/svg`.
- Image files placed in the `src/images` directory will be compressed and optimized with imagemin-mozjpeg (JPEG) or imagemin-pngquant (PNG) and compiled into `dist/images`.
- Files and folders placed in the `copy` directory will be copied as-is into the `dist` directory.

### package.json

The `package.json` file holds all of the details about your project.

Some information is automatically pulled in from it and added to a header that's injected into the top of your JavaScript and CSS files.

```json
{
	"name": "project-name",
	"version": "0.0.1",
	"description": "A description for your project.",
	"main": "./dist/your-main-js-file.js",
	"author": {
		"name": "YOUR NAME",
		"url": "http://link-to-your-website.com"
	},
	"license": "MIT",
	"repository": {
		"type": "git",
		"url": "http://link-to-your-git-repo.com"
	},
	"devDependencies": {}
}
```

_**Note:** `devDependencies` are the dependencies Gulp uses. Don't change these or you'll break things. If any of the other fields are removed, make sure to remove reference to them in the Header (under `var banner` in `gulpfile.js`) or `gulp watch` won't run._

### JavaScript

Put your JavaScript files in the `src/js` directory.

Files placed directly in the `js` folder will compile directly to `dist/js` as both minified and unminified files. Files placed in subdirectories under `src/js` will also be concatenated into a single file. For example, files in `js/detects` will compile into `detects.js`.

_**A note about polyfills:** In subdirectories that contain files with the `.polyfill.js` suffix (for example, `_matches.polyfill.js`), two versions will be created: one with the polyfill files, and one without._

### Sass

Put your [Sass](https://sass-lang.com/) files in the `src/sass` directory.

Gulp generates minified and unminified CSS files. It also includes [autoprefixer](https://github.com/postcss/autoprefixer), which adds vendor prefixes for you.

### SVGs

Place SVG files in the `src/svg` directory.

SVG files will be optimized with [SVGO](https://github.com/svg/svgo) and compiled into `dist/svg`.

### Images

Place images files in the `src/images` directory.

Image files will be compressed and optimized with [imagemin-mozjpeg](https://www.npmjs.com/package/imagemin-mozjpeg) for JPEGs or [imagemin-pngquant](https://www.npmjs.com/package/imagemin-pngquant) for PNGs and compiled into `dist/images`.

### WebP

Place images files in the `src/webp` directory.

Image files will be converted and optimized using [imagemin-webp](https://www.npmjs.com/package/imagemin-webp) and compiled into `dist/webp`.

Image file extensions will be renamed to `.webp` using [gulp-ext-replace](https://www.npmjs.com/package/gulp-ext-replace).

### Copy Files

Files and folders placed in the `src/copy` directory will be copied as-is into `dist`.

This is a great place to put HTML files, images, and pre-compiled assets.

## Options & Settings

Gulp workflow makes it easy to customize for projects without having to delete or modify tasks.

Options and settings are located at the top of the `gulpfile.js`.

### Settings

Set features under the `settings` variable to `true` to turn them on (default), and `false` to turn them off.

```js
/**
 * Settings
 * Turn on/off build features
 */

var settings = {
	clean: true,
	scripts: true,
	polyfills: true,
	styles: true,
	svgs: true,
	images: true,
	webp: true,
	copy: true,
	reload: true,
};
```

### Paths

Adjust the `input` and `output` paths for all of the Gulp tasks under the `paths` variable. Paths are relative to the root project folder.

```js
/**
 * Paths to project folders
 */

var paths = {
	input: 'src/',
	output: 'dist/',
	scripts: {
		input: 'src/js/*',
		// polyfills: '!src/js/*.polyfill.js',
		polyfills: '.polyfill.js',
		output: 'dist/js/',
	},
	styles: {
		input: 'src/sass/**/*.{scss,sass}',
		output: 'dist/css/',
	},
	svgs: {
		input: 'src/svg/*.svg',
		output: 'dist/svg/',
	},
	images: {
		input: 'src/images/*',
		output: 'dist/images/',
	},
	webp: {
		input: 'src/webp/*',
		output: 'dist/webp/',
	},
	copy: {
		input: 'src/copy/*',
		output: 'dist/',
	},
	reload: './dist/',
};
```

### Header

Gulp auto-injects a header into all of your JavaScript and CSS files with details from your `package.json` file.

You can change what's included under the `banner` variable.

```js
/**
 * Template for banner to add to file headers
 */

var banner = {
	full: '/*!\n' + ' * <%= package.name %> v<%= package.version %>\n' + ' * <%= package.description %>\n' + ' * (c) ' + new Date().getFullYear() + ' <%= package.author.name %>\n' + ' * <%= package.license %> License\n' + ' * <%= package.repository.url %>\n' + ' */\n\n',
	min: '/*!' + ' <%= package.name %> v<%= package.version %>' + ' | (c) ' + new Date().getFullYear() + ' <%= package.author.name %>' + ' | <%= package.license %> License' + ' | <%= package.repository.url %>' + ' */\n',
};
```

## License

The code is available under the [MIT License](LICENSE.md).
