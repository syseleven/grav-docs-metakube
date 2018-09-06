# grav-docs-metakube

[![Build Status](https://travis-ci.org/syseleven/grav-docs-metakube.svg?branch=master)](https://travis-ci.org/syseleven/grav-docs-metakube)

SysEleven MetaKube Documentation Repository

The content of master is automatically displayed at [https://docs.syseleven.de/metakube](https://docs.syseleven.de/metakube) in a [Grav CMS](https://getgrav.org/) installation.

## Development

To check if the markdown is valid, node.js markdownlint is used. If you want to run the lint checks locall, you need a current node.js version installed. 

On MacOS with homebrew:

```shell
$ brew install node
```
 
After the initial checkout you have to fetch markdownlint and all of it's dependencies by running

```shell
$ npm install
```  
 
Then run

```shell
$ npm run lint
```

to run the markdownlint on the whole repository.

## Contributions

We welcome contributions and fixes for our documentation. 

It is important that you respect the directory structure pattern already present under `/pages`, so that grav is able to find and link the pages correctly:

* Every page needs one folder
* The folder name maps to the url slug
* The folder name should be prefixed with a number used for ordering the menu items
* Maximum nesting level is 2
* The markdown file with the content within one folder has to be named `default.en.md`
* Links to images and other pages should be relative links to the markdown file, grav will automatically rewrite them correctly

To ensure that there are no syntax errors and that the metadata is correct, run `npm run lint` before creating a pull request.
