# Release preparation checklist

## Prerequisites:

Checkout Invenio cookiecutter template:

```console
$ git clone https://github.com/inveniosoftware/cookiecutter-invenio-module.git
$ cd cookiecutter-invenio-module
$ git checkout feature-betareview
$ cd ..
```

## 1. Analyse module

- [ ] Is I18N required?
- [ ] Is example application required?
- [ ] Is module using an extension class?
- [ ] Determine Cookiecutter variables:
  - Project name: Invenio-FunGenerator
  - Project shortname: invenio-fungenerator
  - Package name: invenio_fungenerator
  - GitHub repository: inveniosoftware/invenio-fungenerator
  - Description: *(oneliner - get from e.g. ``README.rst``)*
  - Author name: CERN
  - Author email: info@inveniosoftware.org
  - Year: *(get earliest year from header of e.g. ``setup.py``)*
  - Copyright holder: CERN
  - Copyright by intergovernmental: True
  - Superproject: Invenio
  - Transifex project: invenio-fungenerator
  - Extension class: *(get from ``ext.py``)*
  - Configuration prefix: *(get from ``config.py`` or ``ext.py``)*


### 1.1 Proof read API
-   [ ] Check open GitHub Issues about module.
-   [ ] Read and understand API.
-   [ ] Check existing API methods for feature completeness and correctness.
-   [ ] Check if new API methods is needed.
-   [ ] Report anything that needs to be fixed on the GitHub repository.

## 2. Generate project structure using latest cookiecutter template

First we generate a new project skeleton structure using the latest
cookiecutter template. We will later compare this skeleton with the current
project structure to detect which files need changes.

First install click and cookiecutter if you don't already have them:

```console
$ mkvirtualenv repocheck
$ pip install click cookiecutter
```

Next, generate the skeleton project structure (note that you will need to have
the cookiecutter variables which was used initially ready - see previous
section):

```console
$ cookiecutter pathto/cookiecutter-invenio-module
```

## 3. Synchronize current project structure with new skeleton structure

Run the ``repocheck.py`` script in the cookiecutter-invenio-module:

```console
$ python cookiecutter-invenio-module/scripts/repocheck.py \
  pathto/current/invenio-fungenerator pathto/skeleton/invenio-fungenerator
```


### 3.1 Check and fix diffs:

Note: The diffs only help to identify which parts *might* not be in sync with
the latest project structure. Changes cannot be applied blindly and should be
be carefully reviewed if needed or not.

-   [ ] ``.editorconfig``
-   [ ] ``.tx/config`` (not needed if module does not require I18N - i.e.
    entire ``.tx`` directory can be removed)
-   [ ] ``AUTHORS.rst``
-   [ ] ``CHANGES.rst``
-   [ ] ``docs/conf.py``
-   [ ] ``INSTALL.rst``
-   [ ] ``{{package_name}}/version.py``
-   [ ] ``MANIFEST.in``
    -   [ ] Should be sorted and without comments from auto-updates
    -   [ ] Should **NOT** contain ``include *.py``
-   [ ] ``README.rst``
-   [ ] ``RELEASE-NOTES.rst``

### 3.2 Check and fix almost identical files:
-   [ ] ``.dockerignore``
-   [ ] ``.gitignore``
-   [ ] ``babel.ini`` (not needed if module does not require I18N)
-   [ ] ``CONTRIBUTING.rst``
-   [ ] ``docs/authors.rst``
-   [ ] ``docs/changes.rst``
-   [ ] ``docs/configuration.rst`` (may need changes if module is e.g. split in
    many extensions)
-   [ ] ``docs/contributing.rst``
-   [ ] ``docs/index.rst``
-   [ ] ``docs/installation.rst``
-   [ ] ``docs/license.rst``
-   [ ] ``docs/make.bat``
-   [ ] ``docs/Makefile``
-   [ ] ``docs/requirements.txt`` (may need changes in order for Sphinx
    documentation to build correctly on Read The Docs).
-   [ ] ``docs/usage.rst``
-   [ ] ``LICENSE``
-   [ ] ``pytest.ini``
-   [ ] ``run-tests.sh``
-   [ ] ``setup.cfg`` (note following sections should be removed if no I18N is
    needed ``compile_catalog``, ``extract_messages``, ``init_catalog``,
    ``update_catalog``)

### 3.3 Check following files manually (by comparing and diff'ing with template):
-   [ ] ``setup.py`` (``repocheck.py`` will check some of the attributes)
    -   [ ] same over all structure for file is used
    -   [ ] package dependencies are sorted in alphabetical order
    -   [ ] ``test_require``: review packages and their version numbers needed
        for tests
    -   [ ] ``extras_require``.
    -   [ ] ``setup_requires`` - Remove ``Babel`` if no I18N is needed.
    -   [ ] ``install_requires`` - Remove ``Flask-Babel`` if no I18N is needed.
    -   [ ] ``setup()`` attributes. Check keywords
    -   [ ] ``entry_points``: Sorted and makes sense?
    -   [ ] ``classifiers``: Ensure Python version classifiers match with tested versions in ``.travis.yml`` as well as
        ``'Development Status :: 4 - Beta'``,
-   [ ] ``.travis.yml``
    -   [ ] ``matrix > fast_finish: true``
    -   [ ] ``python``: Remove ``3.3`` and ``2.6`` if present.
    -   [ ] Check remaining attributes for large discrepancies.
-   [ ] ``tests/conftest.py``
    -   [ ] Check ``instance_path`` and ``app`` fixtures. Should be using
        ``@pytest.yield_fixture`` and ``app`` fixture should create application
        context.
-   [ ] ``examples/app-fixtures.sh``
    -   [ ] Remove if no example app is needed.
    -   [ ] Top of file should be identical. Remaining part should create any
        fixtures needed by the example application.
-   [ ] ``examples/app-setup.sh``
    -   [ ] Remove if no example app is needed.
    -   [ ] Top of file should be identical. Remaining part should setup the
        base application, e.g. create instance directory.
-   [ ] ``examples/app-teardown.sh``
    -   [ ] Remove if no example app is needed.
    -   [ ] Top of file should be identical. Remaining part should clean up
        any files created by the app and setup and fixtures scripts.
-   [ ] ``examples/app.py``
    -   [ ] Remove if no example app is needed.
-   [ ] ``tests/test_examples_app.py``
    -   [ ] Remove if no example app is needed.
    -   [ ] ``example_app`` fixture should be identical.
-   [ ] ``requirements-devel.txt``
    -   [ ] Important package dependencies should be listed here.
    -   [ ] List of modules in``.editorconfig:known_third_party`` should match
        1-to-1 all devel requirements.
-   [ ] ``config.py``
    -   [ ] Check if all variables are used and documented
    -   [ ] Check if any config variables are set up in extension, refactor to config.py if possible.


## 4. Code improvements:
-   [ ] General code improvements
    -   [ ] Check code quality all over the module
    -   [ ] Does QualifiedCode report any errors that can be fixed?
    -   [ ] Is the test coverage % sufficient across the whole module?
    -   [ ] Is all core code (API, models) covered with unit test?
    -   [ ] grep the module for "TODO"/"FIXME". Remove where no longer
        relevant.
-   [ ] Dependencies:
    -   [ ] Remove unused dependencies in ``setup.py`` and
        ``requirements-devel.txt``
    -   [ ] Module should not depend on ``flask_cli`` nor should it instantiate
        ``FlaskCLI`` extension.
    -   [ ] Change double bracket "string" to 'string' if you spot it anywhere.


## 5. Example app:

-   [ ] Example app should be documented in docstring.
-   [ ] Example app should run according to documentation.
-   [ ] Example app documentation should be visible in ReadTheDocs.

## 6. I18N

-   [ ] Are all strings decorated as translation strings?
-   [ ] Have all strings be tested for correct translation - e.g. lazy vs.
    immediate?
-   [ ] Does Transifex project exists (or is it removed if I18N is not needed).
-   [ ] Have message catalogue been uploaded to Transifex.

## 7. Documentation:

### 7.1 Write
-   [ ] General documentation:
    -   [ ] Update feature description in README.rst
    -   [ ] Document usage / getting started guide inside package docstring
        (see [example](https://github.com/inveniosoftware/invenio-db/blob/master/invenio_db/__init__.py)).
    -   [ ] Make config variables self-documented and imported in the package
        docs under 'Configuration' section
        (see [example](https://invenio-celery.readthedocs.io/en/latest/usage.html#module-invenio_celery.config)
        and [example](https://github.com/inveniosoftware/invenio-celery/blob/master/invenio_celery/__init__.py#L25-L35)).
        Note: there's exceptions to this pattern for some modules.
    -   [ ] Are all API methods, classes and modules plugged in the documentation as autodoc?
    -   [ ] Add intersphinx mappings where convenient and make sure they work in compiled documentation
        (see [reference](https://github.com/inveniosoftware/invenio-admin/blob/master/invenio_admin/ext.py#L119)
        and [config](https://github.com/inveniosoftware/invenio-admin/blob/master/docs/conf.py#L332-L337)).
    -   [ ] Are chapter/section underline (i.e. ``====`` or ``-----``) matching the length of the section/chapter title?
-   [ ] Functional documentation
    -   [ ] Document all public methods (docstrings with description and types).
    -   [ ] Methods and functions that accept ``**kwargs`` should document
        potential arguments and mention if they are passed to another
        function/method which provides documentation for them.
    -   [ ] Document signals.

### 7.2 Review (read the compiled documentation A-Z)
-   [ ] Is all imported autodoc documentation being loaded correctly?
-   [ ] Do all pages and sections contain some text?
-   [ ] Are all links on the page (API class references, intersphinx links)
    resolvable?
-   [ ] Are all text and characters rendered correctly?
    -   Non-breaking newlines going outside the page?
    -   Strange characters, half-escaped style markers inside docstrings
    (*, **, )
-   [ ] Docs are compiled and assembled from multiple file/package docstings
    into single pages - is the assembled block of text easy to read? Unify the
    compiled text if possible.
-   [ ] Create a new virtual environment and follow the installation and then
    getting started guide - does it actually work as described? Add and improve
    documentation if the description is missing something.
