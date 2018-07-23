# grav-docu-metakube

![travis.ci Status](https://api.travis-ci.org/syseleven/grav-docu-metakube.svg?branch=master)

SysEleven MetaKube Documentation Repository

The content of master is automatically displayed at [https://docs.syseleven.de/metakube](https://docs.syseleven.de/metakube) in a [Grav CMS](https://getgrav.org/) installation.

## Development

To check if the markdown is valid run:

```bash
$ npm run lint
```

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