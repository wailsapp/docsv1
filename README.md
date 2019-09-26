# docsv1
[WIP] Docs for the V1 release

## DEV TEST/BUILD

The documentation site is built using the [Hugo](https://gohugo.io/) static site generation tool. To test, preview or get a real feel for the site, you will need to build the site from this repository.

This short document assumes that you are familiar with `git` and the associated shell tools for your platform. Please note that the initial example commands are based on Linux (but should work on a Mac). I would encourage those who have access to these systems to add the appropriate command samples to this document.

* First install Hugo by following the docs on the [Hugo site](https://gohugo.io/getting-started/installing#readout).
* Clone this repository to the desired location on your local system with something like `cd /path/to/clone/repo && git clone https://github.com/wailsapp/docsv1.git`.
* Change to the cloned repository with `cd docsv1`.
* Run the `hugo` command that runs the dev server with `hugo server`.
* The resulting command will display a localhost address (i.e. http://localhost:1313/) of which you can open in your local web browser (to preview the site).
 

### Condensed Command Sample

```
cd /path/to/clone/repo
git clone https://github.com/wailsapp/docsv1.git
cd docsv1
hugo server
```
