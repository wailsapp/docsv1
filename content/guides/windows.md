+++
title = "Developing for Windows"
date = 2019-08-29T04:56:50+10:00
weight = 14
chapter = false
+++

Compared to the other platforms, developing for Windows is a little trickier for the following reasons:

  * The Windows builds use mshtml, which is essentially IE11 (NOTE: We are moving to WebView2 for Wails V2 which will eliminate almost all of the issues discussed on this page)
  * Mshtml does not have a developer console which can make debugging Javascript fairly difficult


## IE11 Compatibility

The best way to ensure your app will work correctly when building is to test using the `wails serve` command and using IE11 to test your app. IE11 is quite behind in many respects so extra attention has to be paid to ensure your code works correctly, such as:

  * Adding polyfills for modern Javascript constructs.
  * Using alternatives for modern HTML APIs

_Note_: mshtml might be close to IE11, but it is not an exact similar environment, some features and browser capabilities might be unavailable in your wails powered app, such as localStorage.  

During development, remember to make sure that `npm run start` creates an IE11 compatible build to be able to use it and preview your app, you can do that by adding this to your `frontend/package.json`:    
```json
"browserslist": {
    "production": [
        ">0.2%",
        "not dead",
        "not op_mini all",
        "ie 11"  
    ],
    "development": [
        "last 1 chrome version",
        "last 1 firefox version",
        "last 1 safari version",
        "ie 11"
    ]
}
```

## Javascript polyfills  

This is a collection of recommended polyfill packages you can use

| Package | Website |
| ---------------------- | ------------ |
| core-js  | [Documentation (npm)](https://www.npmjs.com/package/core-js) |
| react-app-polyfill     | [Documentation (npm)](https://www.npmjs.com/package/react-app-polyfill) |

## HTML API Alternatives

This is a collection of IE11 compatible libraries 

| Incompatible Construct | Alternatives |
| ---------------------- | ------------ |
| `<input type="data">`  | [JQuery Datepicker](https://jqueryui.com/datepicker/) |

## Troubleshooting

### My app works with `wails serve` but not `wails build`. Why?

Most likely, your application is using a non-IE11 compatible HTML or Javascript construct. Other causes can be the use of "browser APIs" that are not available in the mshtml webview, such as localstorage.

### When running the `<project>.exe` a script error window pops up. Why?

This is similar to the previous issue, make sure to run `wails serve` with devtools open to check that your app renders correctly. If the app renders in IE11 and does not after building, it is possible that you are using a browser feature that exists in IE11 and not in mshtml (ie. localstorage)

