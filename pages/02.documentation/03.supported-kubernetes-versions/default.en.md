---
title: 'Supported Kubernetes Versions'
published: true
date: '18-07-2018 13:00'
taxonomy:
    tag:
        - kubernetes
        - metakube
---

We automatically offer new Kubernetes versions once they are released and tested by us. For the creation of new clusters we aim to support:

* The latest two versions of the latest tested non x.x.0 release
* The latest version of the previous two tested non x.x.0 releases
* The x.x.0 release of the latest version (if newest and tested)

The default version for new clusters is the latest tested non x.x.0 release. Existing clusters will continue to run and can continue to be upgraded even though their Kubernetes version might not be supported for new clusters anymore.

## Examples

unsupported, **supported**, **<u>default</u>**

| v.1.8.x | v.1.9.x | v.1.10.x | v.1.11.x |
| :----- | :----- | :----- | :----- |
| ..., v1.8.14, **v1.8.15** | ..., v1.9.8, **v1.9.9** | ..., v1.10.3, **v1.10.4**, **<u>v1.10.5</u>** | |
| ..., v1.8.14, **v1.8.15** | ..., v1.9.8, **v1.9.9** | ..., v1.10.3, **v1.10.4**, **<u>v1.10.5</u>** | **v1.11.0** |
| ..., v1.8.14, **v1.8.15** | ..., v1.9.8, **v1.9.9** | ..., v1.10.3, v1.10.4, **v1.10.5**, **<u>v1.10.6</u>** | **v1.11.0** |
| ..., v1.8.14, v1.8.15 | ..., v1.9.8, **v1.9.9** | ..., v1.10.3, v1.10.4, v1.10.5, **v1.10.6** | **v1.11.0**, **<u>v1.11.1</u>**          |
| ..., v1.8.14, v1.8.15 | ..., v1.9.8, **v1.9.9** | ..., v1.10.3, v1.10.4, v1.10.5, **v1.10.6** | v1.11.0, **v1.11.1**, **<u>v1.11.2</u>** |
