# Gulp Starter Kit
A simple Gulp site setup for those looking to spin up a simple local website environment. This starter does the following things:

 1. **Jekyll**
  - Builds a local static website.
  - Watches all files associated with Jekyll (HTML, YML, JS) and rebuilds the site on file save.
 2. **CSS**
  - Compiles user-defined SCSS/SASS files into two CSS files: a readable and a minified version.
  - During compilation, the following items are used:
     - **PreCSS:** A PostCSS plugin that allows you to use Sass-like markup in CSS. ([Link](https://github.com/jonathantneal/precss))
     - **PostCSS Preset Env:** Converts modern CSS by determining polyfills reqired based on browser or runtime environment targets. ([Link](https://github.com/csstools/postcss-preset-env))
     - **PostCSS Sorting:** Allows you to re-orders CSS properties so they are consistently in the same order across files. ([Link](https://github.com/hudochenkov/postcss-sorting))
     - **PostCSS Normalize:** Imparts the pieces of the normalize reset file required based on targeted browsers. ([Link](https://www.npmjs.com/package/postcss-normalize))
     - **cssnano:** Minifies the compiled CSS. ([Link](https://github.com/cssnano/cssnano))
  - Watches specified files / folders for SCSS, SASS, or CSS file changes and recompiles CSS.
  3. **BrowserSync**
     - Uses BrowserSync to serve up the compiled Jekyll static website.
     - Automatically reloads all browsers viewing the website when files are saved and re-compiled.
     - Syncs browser windows together, allowing you to scroll in one and mimc the same action in other browser windows on your machine or other machines.


# Getting started

## Install Ruby

To run this website, you will need a full Ruby development environment. If you already have this, you can skip this step.

### macOS

 1. Install Xcode by either downloading it [directly](https://developer.apple.com/xcode/) or through the Mac App Store. *Warning: This is a few GB.*
 2. Open a Terminal window (or CLI tool of choice) and install Xcode command line tools: `xcode-select --install`
 3. Accept the Xcode license either by opening Xcode or typing in: `sudo xcodebuild -license accept`
 4. Install Ruby using [RVM](https://rvm.io/): `\curl -sSL https://get.rvm.io | bash -s stable --rails`
 5. Exit Terminal / CLI tool and relaunch it to ensure your session is aware of new dependencies installed.

### Windows
Download and install a Ruby+Devkit version from [RubyInstaller Downloads](https://rubyinstaller.org/). You’ll want to restart your computer before moving on.

After you've installed Ruby, do the following steps. Skip any step you don't need.

 1. Clone this repo using command line: `git clone https://github.com/hellohynes/gulp-starter-kit.git`
 2. Install [Node & NPM](https://nodejs.org/en/download/). Skip if you already have this installed.
 3. Navigate to this repo folder in your CLI window.
 4. Type `npm install` to install NPM dependencies.
 5. In the `/docs` folder, type `gem install bundler` to install Bundler.
 6. Now install Gems by typing `bundle install`.
 7. Navigate back to your root folder (`cd ../`) and type `gulp`.
 8. Visit the website at `https://localhost:4000`.

> **Note:** This website provides a split folder structure, ideal for websites which have source files and then a corresponding documentation website explaining said source files. If you don't want this dual folder structure, you will need to [customize a few items, outlined below](#customizations).


# Customizations

To get started, you will need to make some modifications within the `/gulp/config.js` to the following items:

| `vars` | Description |
| --- | --- |
| `src` | Where your website's files are stored. It is recommended you proceed the directory with a `./`. This tells BrowserSync to look for your folder starting from the root directory.|
| `dev` | Where your compiled Jekyll website is. The default location is a `/_site` folder within your `src` location, unless you've changed this within your Jekyll `_config.yml`.|
| `lib` | This assumes splitting your website (e.g. documentation) from source files. If so, this is the location of your source. **If you aren't bifurcating files, remove this.** |
| `assets` | Your website assets folder.|
| `jekyll: config` | Location of your Jekyll config file. You only need to specify this if it _isn't_ located in your website's root folder. **If the `_config.yml` file is located in the root foler, remove this line.** |
| `jekyll: baseurl: ''` | If your GitHub Page address has a folder in the URL (i.e. `https://[your-site].github.io/[folder-name]), you might not need that `[folder-name]` locally. If you don't, this setting overrides your `_config.yml` setting so it works locally and in production. **If your folder is the same locally with production, remove this line.** |
| `css: scsslib` | Locations of SCSS / SASS source file(s) to be compiled. By default this will try to compile every file located in your `/scss` folder. It is recommended you have it compile your main file only. **If you don't have source files, remove the `scsslib` line.** |
| `css: scssdocs` | Locations of SCSS / SASS website file(s) to be compiled. By default this will try to compile every file located in your `/scss` folder. It is recommended you have it compile your main file only. |
| `css: cssLib` | Location of compiled CSS source files. **If you don't have source files, remove the `scsslib` line.** |
| `css: cssdocs` | Location of compiled CSS website files.|
| `watch: jekyll` | List all files that gulp will watch for changes on. Default is the config file; all HTML, markdown, and data files; and *not* files in the `/_site` folder (you could get some horrible looping happening otherwise). |
| `watch: libcss` | Watch for changes in source files. **If you don't have source files, remove this line.** |
| `watch: doccss` | Watch for changes in the website's `/scss` folder. |
| `watch: js` | Watch for changes in `/js` folder. |
| `watch: images` | Watch for changes in `/images` folder. |
| `watch: fonts` | Watch for changes in `/fonts` folder. **If you aren't hosting your own fonts, remove this line.** |

If you aren't splitting your website up between source and website files, you will need to a few more modifications.

Remove the following line from `gulp/tasks/watch.js`:

    gulp.watch(config.libcss, ['css']);

Remove the following lines from `gulp/tasks/css.js`:

    gulp.task('lib-css', function() {
        browsersync.notify('Compiling Dialtone CSS...');

        return gulp.src(config.scsslib)
            .pipe(sass().on('error', sass.logError))
            .pipe(postcss())
            .pipe(gulp.dest(config.csslib))
            .pipe(gulp.dest(config.cssdocs))
            .pipe(postcss([
                normalize({ forceImport: true }),
                cssnano()
            ]))
            .pipe(rename({ suffix: '.min' }))
            .pipe(gulp.dest(config.csslib))
            .pipe(gulp.dest(config.cssdocs));
    });

If you removed the `jekyll: baseurl` or `jekyll: config` settings in `gulp/config.js`, then remove the corresponding line in `gulp/tasks/jekyll.js`:

    '--config=' + config.config,
    '--baseurl=' + config.baseurl
