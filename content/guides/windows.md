+++
title = "Developing for Windows"
date = 2019-08-29T04:56:50+10:00
weight = 14
chapter = false
+++

Compared to the other platforms, developing for Windows is a little trickier for the following reasons:

  * The Windows builds use mshtml, which is essentially IE11
  * Mshtml does not have a developer console which can make debugging Javascript fairly difficult


## IE11 Compatibility

The best way to ensure your app will work correctly when building is to test using the `wails serve` command and using IE11 to test your app. IE11 is quite behind in many respects so extra attention has to be paid to ensure your code works correctly, such as:

  * Adding polyfills for modern Javascript constructs, EG: Promises (We recommend using corejs!)
  * Using alternatives for modern HTML APIs

## HTML API Alternatives

This is a collection of IE11 compatible libraries 

| Incompatible Construct | Alternatives |
| ---------------------- | ------------ |
| `<input type="data">`  | [JQuery Datepicker](https://jqueryui.com/datepicker/) |
| Modern JS features     | [corejs](https://github.com/zloirock/core-js) |

## Troubleshooting

### My app works with `wails serve` but not `wails build`. Why?

Most likely, your application is using a non-IE11 compatible HTML or Javascript construct. Other causes can be the use of "browser APIs" that are not available in the mshtml webview, such as localstorage.

