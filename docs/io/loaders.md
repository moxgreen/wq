---
order: 3
---

wq.io: Loaders
==============
[wq.io.loaders]

[wq.io]'s `Loader` [mixin classes] facilitate loading an external resource from the local filesystem or from the web into a file-like object.  A loader is essentially just a class with `load()` and `save()` methods defined.  The canonical example is `FileLoader`, represented in its entirely below:

```python
class FileLoader(BaseLoader):
    filename = None
    read_mode = 'r'
    write_mode = 'w+'

    def load(self):
        try:
            self.file = open(self.filename, self.read_mode)
            self.empty_file = False
        except IOError:
            self.file = StringIO()
            self.empty_file = True

    def save(self):
        file = open(self.filename, self.write_mode)
        self.dump(file)
        file.close()
```

As can be seen above, every `Loader`'s `load()` method should take no arguments, instead determining what to load based on properties on the class instance.  (Remember that the [BaseIO] class provides a convenient method for setting class properties on initialization).  `load()` should set two properties on the class:

 * `file`, a file-like object that will be accessed by the parser
 * `empty_file`, a boolean indicating that the file was empty or nonexistent (used to short-circuit avoid parser errors)

To support file output, loaders should define a `save()` method, which should prepare a file-like object for writing, call `self.dump()` with the output file, and perform any needed wrap-up operations.

### Built-In Loaders

There are four main built-in loader classes defined in [wq.io.loaders].

 * `FileLoader` and `BinaryFileLoader`, which load data from the local filesystem, and expect a `filename` property to be set on the class instance.
 * `StringLoader`, which loads data to and from a `string` property that should be set on the class instance.
 * `NetLoader`, which loads data over HTTP and expects a `url` property to be set or computed by the class or instance.

`NetLoader` also supports optional `username`, `password`, and URL `params` properties.  Note that `save()` is not implemented for `NetLoader`.

### Custom Loaders

The built in loaders should be enough for many use cases.  The most common use for a custom loader is to encapsulate a number of `NetLoader` options into a reusable mixin class.  For example, the [climata library] defines a `WebserviceLoader` for this purpose.

[wq.io.loaders]: https://github.com/wq/wq.io/blob/master/loaders.py
[wq.io]: http://wq.io/wq.io
[mixin classes]: http://wq.io/docs/custom-io
[BaseIO]: http://wq.io/docs/base-io
[climata library]: https://github.com/heigeo/climata
