# django-adv-cache-tag
## Django advanced cache template tag

### Quick !

* versioning
* compress
* partial caching
* extend/customize easily

### Details ?

With `django-adv-cache-tag` you can :

* add a version number (int, string, date or whatever, it will be stringified) to you templatecache : the version will be compared to the cached one, and the exact same cache key will be used for the new cached template, avoiding keeping old unused keys in your cache, allowing you to cache forever.
* avoid to be afraid of an incompatible update in our algorithm, because we also use an internal version number, updated only when this algorithm changes
* compress the data to be cached, to reduce memory consumption in your cache backend, and network latency (but it will use more time and cpu to compress/decompress)
* choose which cache backend will be used
* define your own cache keys (or more simple, just add the primary key (or what you want, it's a templatetag parameter) to this cache key
* define `{% nocache %}...{% endnocache %}` blocks, inside your cached template, that will only be rendered when asked (for these parts, the content of the template is cached, not the rendered result)
* easily define your own algorithm, as we provide a single class you can inherit from, and simply change options or whatever behaviour you want, and define your own tags for them

This project is an early alpha-stage, with no documentation, no tests, no package.

But if you find it usefull, feel free to participate !

## Simple usage

```django
{% load adv_cache %}

<p>Below is a cached template for {{ obj }}, from class "klass",

{% cache 0 klass obj.pk 1 %}
<blockquote>
    <p>This is the cached part of the template for {{ obj }}, evaluated at {% now "r" %}.</p>
    {% nocache %}
        <p>This part will be evaluated each time : {% now "r" %}</p>
    {% endnocache %}
    <p>This is another cached part</p>
</blockquote>
{% endcache %}
```
This (with all options activated) will generate a new entry in your cache backend with the key (by default): `template.adv-cache.klass.3.xyz` (3 is the obj's pk, and xyz, a hash for this pk [you can add more arguments here, as for the django cache templatetag).

The last argument, here `1`, is your version of this cache. It can be set in the template, or can be of field of an object (`obj.last_updated`...) or what you want. This version is not included in the arguments used to compute the hash.

This cached template will stay in your cache for ever, and will be regenerated if :

* you delete the key or empty the cache backend...
* the version included in the template is updated
* the internal version of `django-adv-cache-tag` is updated
* options for the default cache mode are updated