# Browser Extension Webpack Boilerplate

A basic boilerplate for creating [WebExtensions API-based Browser Extensions](https://developer.mozilla.org/en-US/Add-ons/WebExtensions) using [Webpack](https://webpack.js.org/).

## Supported browsers

Extensions created this way are, at least in theory, compatible with the following browsers:

-   Google Chrome
-   Chromium
-   Mozilla Firefox
-   Firefox for Android
-   Opera
-   Microsoft Edge

Actual compatibility will depend on the APIs you used. [MDN provides a comprehensive comparison](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Browser_support_for_JavaScript_APIs).

While [Chrome's APIs currently differ](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Porting_a_Google_Chrome_extension)
from the standard, this boilerplate utilises [webextension-polyfill](https://github.com/mozilla/webextension-polyfill)
which allows you to seamlessly use the `browser.*` APIs regardless of browser.
The polyfill is automatically included by Webpack when needed.

## Webpack rules
The Webpack configuration currently handles the following file types:

-   **.jpg .jpeg, .png, .gif, .eot, .otf, .svg, .ttf, .woff and .woff2** are all
    treated as static assets using [file-loader](https://github.com/webpack-contrib/file-loader).
-   **.html** files are parsed using [html-loader](https://github.com/webpack-contrib/html-loader)
    - they are minified and `<link>`, `<img>` and `<script>` files are used to
    find other files that need to be parsed.
-   **.css** files are parsed using [css-loader](https://github.com/webpack-contrib/css-loader).
-   **.js** files are treated differently depending on their file name:
    -   Files named **index.js** are treated using [spawn-loader](https://github.com/erikdesjardins/spawn-loader).
        In other words, *each **index.js** is treated a separate entry-point*,
        with all of its dependencies bundled resolved and bundled separately
        and independently from other files.
    -   Any other **js** file will be transpiled using Babel and treated as a
        normal dependency, bundled with the files that `import` it.
-   **manifest.json** is the main entry point. Other files (icons, scripts,
    pages) referred inside of it will be separately bundled by Webpack depending
    on the rules above.

## Repo structure

### webpack.config.js
The main [Webpack configuration](https://webpack.js.org/configuration/) of the
project. Unlike most web project, the entry point of this project is the
**manifest.json** file - which is also the first file read by the browser and
technically the only required file, per the WebExtensions standard.

### package.json
The [Node.js package.json](https://docs.npmjs.com/files/package.json) file is
required by Webpack - it contains the ["dev dependencies"](https://docs.npmjs.com/files/package.json#devdependencies)
of the project - most notably, Webpack itself and the various loaders used.

### src/manifest.json
The manifest.json file, [as defined in the WebExtensions standard](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/manifest.json).

Many of the properties, such as the *name* and *author* fields, are pulled from
**package.json** using [prop-loader](https://github.com/erikdesjardins/prop-loader),
to avoid having these diverge from each other.

Keep in mind that, since the **manifest.json** is the only entrypoint, it's also
what tells Webpack which other files - most notably, HTML and JS - need to be
compiled.

### src/background
Contains the script running as [background script](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Anatomy_of_a_WebExtension#Background_scripts)
for the extension. Since scripts are bundled with Webpack, it's advisable to
have only a single entry script here, **index.js**, which loads other
dependencies using `import` statements (as seen with importing the `getOS`
function from **getos.js**).

### src/content
Contains [content script(s)](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/Content_scripts)
injected into other web pages.

Content scripts can be injected using the `content_scripts` property in
**manifest.json** or by calling the [executeScript](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/API/Tabs/executeScript)
method on a tab. In this set-up, the latter is used and the content script is
injected from **src/popup/index.js**.

If you have more than one content script, you must put each in a separate
directory, as the entry point of content scripts (like with other stand-alone JS
files) should always be named **index.js**.

### src/icon
The main extension icon, as referenced in **manifest.json**. Different browsers
have different size requirements, so it's advisable to consult the documentation
of the browser(s) you're targetting to ensure all the required sizes are
provided.

### src/popup
The [popup dialog](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/user_interface/Popups).
In this set-up, the popup dialog is defined in **manifest.json** to appear when
the browser action is pressed - this is normally a button that's always visible
next to the address bar.

This folder also serves to demonstrate a "full" page, with its own HTML, JS and
CSS file. 

### src/options
The [options page](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/user_interface/Options_pages).
In this set-up, a static html file that has no scripts attached to it. The same
set-up from the popup dialog can be used to add CSS and JS files to it.

### global.css
A global CSS file - this file isn't directly referred to in **manifest.json**,
but is used by several other HTML files. The purpose is to demonstrate how
this set-up deals with shared resources.

## Developing a new extension
*It's highly recommended to [get familiar with Webpack](https://webpack.js.org/guides/getting-started/),
and [read about WebExtensions on MDN](https://developer.mozilla.org/en-US/Add-ons/WebExtensions)
before you continue.*

1.  [Install Node.js](https://nodejs.org/en/download/).
1.  Install Webpack: `npm install -g webpack`
1.  Clone this repository.
1.  Remove CONTRIBUTING.md, README.md and, optionally, replace LICENSE with a
    compatible license.
1.  Change the package's name and description on `package.json`
1.  Change the name of your extension on `src/manifest.json`
1.  Run `npm install`
1.  Run `npm run start`
1.  Load your extension on Chrome following:
    1.  Navigate to [chrome://extensions/](chrome://extensions/)
    1.  Check `Developer mode`
    1.  Click on `Load unpacked extension`
    1.  Select the `dist` folder.
1.  Have fun.

## Contributing
Patches / contributions are welcome! Please read CONTRIBUTING.md to find out
more.
