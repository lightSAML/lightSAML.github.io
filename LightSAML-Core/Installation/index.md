---
    title: LightSAML Core Installation
    aside: aside-lightsaml.html
    active: installation
    github: https://github.com/lightSAML/lightSAML
---


It is recommended that you install LightSaml with [composer](https://getcomposer.org/).

Add a requirement to your project composer.json with concrete version

```json
# composer.json
{
    "require": {
        "lightsaml/lightsaml": "^2.0"
    }
}
```

And then run from the command line composer update

```bash
$ composer.phar update lightsaml/lightsaml
```

Or just let the composer choose the version

```bash
$ composer.phar require lightsaml/lightsaml
```

After that you will have LightSaml installed in your vendor directory.

If you don't prefer composer, you can download source code directly from
[LightSAML github repository](https://github.com/lightSAML/lightSAML).
