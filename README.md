## MOBILE MAP STARTER 2

A starting framework for mobile maps using Cordova/Phonegap. A minimal but functional, standalone mobile app from which to build your own creations.

* A combination of AngularJS and Bootstrap forming a basic framework
* Page-turning behavior
* Working Leaflet map with various bug workarounds
* L.TileLayer.Cordova for offline tile caching
* Simple in-code documentation for customization, including pitfalls and gotchas

You may also be interested in https://github.com/gregallensworth/MobileMapStarter and https://github.com/GreenInfo-Network/IonicMapStarter as starting places for your mobile app. We do not actively maintain them anymore, but they still work.



## QUICK START

Clone the repository, then delete its .git to detach it from MMS2:
```
git clone https://github.com/GreenInfo-Network/MobileMapStarter2.git MyShinyNewApp
cd MyShinyNewApp
rm -rf .git
```

Install NPM packages, for this Cordova container and also for your own `www/` content:
```
yarn -i
( cd www/ ; yarn -i )
```

Build the WWW content that is your app. This will also run `cordova prepare` for you to collect the generated files into the app:
```
npm run build
```

Build the icons and splash screens:
```
npm run icon
npm run splash
```

If you will be building for iOS, use Xcode to make some settings changes:
* Open the project file in Xcode.
* In its General tab:
  * Device Orientation = check all 4
  * Deployment target = 8.1 or later
  * Hide Status Bar = checked
* In its Info tab, set/create the following.
  * Status bar is initially hidden = YES
  * View controller-based status bar appearance = NO

Now start customizing to make it your app:
* Edit `config.xml` and `package.json` to reflect you app's name, author, etc.
* And start reorganizing the `www/` content to make your own app.



## DEVELOPMENT OVERVIEW AND CONVENTIONS

This app uses webpack to compile ES2017 code into JavaScript (via Babel `env` preset), and to compile SASS into CSS (via sass-loader and node-sass). The output files are *bundle.css* and *bundle.js* but you should instead make your changes to the `index.*` entry points. See the Quick Start for an overview of the files.

The basic HTML/JS framework is *mobile-angular-ui* This is basically AngularJS and an optimzied build of Bootstrap 3. Read more: http://mobileangularui.com/docs/

Page-changing is done via mobile-angular-ui's *SharedState* service. See the `whichPage` variable in `index.html` for how the pages are turned, and see `ui-set whichPage` in the `sidebar.html` and `navbar-bottom.html` for examples of triggering the page change.

Icon and splash screen are generated from *splash.png* and *icon.png*  See their repositories' documentation for more info: [cordova-splash](https://github.com/AlexDisler/cordova-splash) and [cordva-icon](https://github.com/AlexDisler/cordova-icon)

The essential commands:
* `npm run build` -- Compile the SASS and JS files.
* `npm run watch` -- Compile the SASS and JS files, then watch them for changes and recompile as needed.
* `npm run icon` -- Generate a new set of app icons from *icon.png*
* `npm run splash` -- Generate a new set of app splash screens from *splash.png*

The `yarn.lock` specfies Cordova as a development dependency, meaning that multiple apps would effectively get their own installation of Cordova under their `node_modules/` folder. As such, it is advised that you use the `npm run` commands described, or take care to run `The `yarn.lock` specfies Cordova as a development dependency, meaning that multiple apps would effectively get their own installation of Cordova under their `node_modules/` folder. As such, it is advised that you run `The `yarn.lock` specfies Cordova as a development dependency, meaning that multiple apps would effectively get their own installation of Cordova under their `node_modules/` folder. As such, it is advised that you either write new npm scripts in `package.json` or else specifically use `./node_modules/.bin/cordova` to ensure that you are using the correct version of Cordova for this app.

An overview of the files:
* `www/index.js` -- The entry point for webpack
  * This should act as an entry point to `require()` your SCSS, HTML, etc.
  * Basic entry points would be controller.js, index.scss, etc. but if you use watch mode you may also want to add the templates under `html/` so the watch will recompile when those change.  
* `www/code/*.js` -- Additional JavaScript code to support `index.js`
  * This is a good place to put your JavaScript code.
* `www/index.html` -- The skeleton of the app's HTML rendering.
  * This includes the various script and link tags for loading CSS and JS libraries, and provides the basic layout.
  * For the most part, this is a skeleton and you'll want to work within the `html/*.html` templates for specific panels/pages within your app.
* `www/html/*.html` -- Partial view templates interpolated into the `ng-view` section of `index.html`.
  * This is closely connected to the `angular-ui-router` system, and to routes defined in `index.js` You will probably have one partial view per route.
  * You may want to `require()` these in `index.js` Webpack won't do anything with them, but if you use watch mode and it is listed, then changing the file will trigger a recompile.
* `www/index.scss` -- SASS stylesheet starting point.
  * The entry point for SASS styles. Depending on your preference, you may want to have it `@import` in a modular fashion from the `style/*.scss` files.
* `www/style/*.scss` -- SASS stylesheets.
  * Provided as a convention, in case your workflow fits well with breaking up a monolithic `index.scss` insto smaller chunks.
* `splash.png` -- This will be cropped and resized to generate the app's splash screen for various platforms and devices. Read more: [cordova-splash](https://github.com/AlexDisler/cordova-splash)
* `icon.png` -- This will be cropped and resized to generate the app's icon for various platforms and devices. Read more: [cordva-icon](https://github.com/AlexDisler/cordova-icon)



## OTHER DETAILS AND NOTES

The app directory has a *yarn.lock* file for installing packages related to webpack and compiling JS/SASS. The `www/` folder has a separate `yarn.lock` file, for dependencies used in the web app. Keeping them separate makes for easier maintainability of the www components versus the build system, should you have your own preferred build system. For your use case, perhaps merging and then require()'ing the libs makes sense.

After making changes to the HTML/CSS/JS files, the apps won't be rebuilt by Cordova (and thus visible to Xcode and Android Studio) until you issue a `cordova prepare` If you're using `npm run build` then it will already run a `cordova prepare` for you.

The page-changing system using *SharedState* was not my first choice, but ngRoute and ui.router both provided unsuitable. Ultimately, I used a combination of `ui-show` and `ng-include` behaviors to create a similar effect, but without the problems.
  * They lazy-load partial views so elements are not in the DOM at startup. For code which expects to attach behaviors such as Leaflet, this is unhealthy.
  * The partial view content is unloaded from the DOM when it leaves visibility. This means map tiles, map state, etc. are effectively lost. While a workaround could be to destroy and reinitialize the whole map every time you visit it... no.

