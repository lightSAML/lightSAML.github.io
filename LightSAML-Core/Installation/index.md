---
    title: LightSAML Core Installation
    aside: aside-lightsaml.html
    active: installation
    github: https://github.com/lightSAML/lightSAML
---


It is recommended that you install LightSaml with [composer](https://getcomposer.org/).

Add a requirement to your project composer.json with concrete version

{% highlight json %}
# composer.json
{
    "require": {
        "lightsaml/lightsaml": "^2.0"
    }
}
{% endhighlight %}

And then run from the command line composer update

{% highlight bash %}
$ composer.phar update lightsaml/lightsaml
{% endhighlight %}

Or just let the composer choose the version

{% highlight bash %}
$ composer.phar require lightsaml/lightsaml
{% endhighlight %}

After that you will have LightSaml installed in your vendor directory.

If you don't prefer composer, you can download source code directly from
[LightSAML github repository](https://github.com/lightSAML/lightSAML).
