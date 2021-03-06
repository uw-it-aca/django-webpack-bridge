# Django Webpack Bridge

[![Test, Publish](https://github.com/uw-it-aca/django-webpack-bridge/actions/workflows/cicd.yml/badge.svg)](https://github.com/uw-it-aca/django-webpack-bridge/actions/workflows/cicd.yml) [![PyPI](https://img.shields.io/pypi/v/django-webpack-bridge)](https://pypi.org/project/django-webpack-bridge/) [![npm](https://img.shields.io/npm/v/django-webpack-bridge)](https://www.npmjs.com/package/django-webpack-bridge) ![PyPI - Django Version](https://img.shields.io/pypi/djversions/django-webpack-bridge) ![npm peer dependency version](https://img.shields.io/npm/dependency-version/django-webpack-bridge/peer/webpack) ![PyPI - Python Version](https://img.shields.io/pypi/pyversions/django-webpack-bridge) ![PyPI - License](https://img.shields.io/pypi/l/django-webpack-bridge)

Creates a bridge from webpack to django.

## How to Use

1. Add `django-webpack-bridge` to `setup.py` or `pyproject.toml`, and `package.json`.
2. Add the plugin to `webpack.config.js`

    ```js
    const DjangoBridgePlugin = require('django-webpack-bridge');
    module.exports = {
        ...,
        plugins: [
            ...,
            new DjangoBridgePlugin(),
        ],
    }
    ```

    `django-webpack-bridge` will use the variables from `module.exports.output`
3. Add the following to `settings.py`

    ```python
    INSTALLED_APPS += [
        'webpack_bridge',
    ]
    STATICFILES_DIRS = [
        '{same as module.exports.output.path from webpack.config.js}',
    ]
    ```

4. In the `template.html`

    ```jsx
    {% load webpack_bridge %}
    {% render_webpack_entry 'entry point name' js='defer' %}
    ```

## Settings

```python
BRIDGE_SETTINGS = {
    # Name of the manifest file
    'manifest_file': 'manifest.json',
    # Boolean to turn caching on and off
    'cache': not settings.DEBUG,
    # Timeout duration for the cache
    'cache_timeout': 86400,  # 1 Day
    # Namespace for the cache
    'cache_prefix': 'webpack_manifest',
    # Maps a tag group to a group of tags
    'group_to_extensions': {
        'script': ('js', ),
        'style': ('css', ),
    },
    # Maps a tag group to a html tag
    'group_to_html_tag': {
        'script': '<script src="{path}" {attributes}></script>',
        'style':
            '<link rel="stylesheet" type="text/css"'
            + ' href="{path}" {attributes}>',
    },
    # Time between updaing the manifest from the file while compiling
    'compiling_poll_duration': 0.5,
}
```

`group_to_extensions` and `group_to_html_tag` combine to create a multi-key map from a group of file extensions to a html tag. Eg. `(js, jsx) -> <script src="{path}" {attributes}></script>`

`path`: Will be replaced with the bundle path
`attributes`: Will be replaced with any attributes specfied when when calling 'render_webpack_entry'. Attributes are grouped by file extension

The following settings can be passed to `DjangoBridgePlugin`

```js
{
    path: 'defaults to module.exports.output.path',
    publicPath: 'defaults to module.exports.output.publicPath',
    fileName: 'defaults to manifest.json',
}
```

## Development

### Running tests

1. Create and activate a python virtual env of your choice (optional).
2. Run `pip install .`
3. Run `DJANGO_SETTINGS_MODULE=test_files.settings python -m django test webpack_bridge`

### Running the Demo

1. Run `docker-compose up --build`
2. Open `localhost:8000`
